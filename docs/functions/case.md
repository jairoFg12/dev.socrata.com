---
layout: with-sidebar
sidebar: documentation
title: case(...)

type: function
datatypes:
- checkbox
- double
- floating_timestamp
- line
- location
- money
- number
- point
- polygon
- text 
description: Returns different values based on the evaluation of boolean comparisons
---

{% include function_header.html function_name=page.title description=page.description datatypes=page.datatypes %}

The `case(...)` function is a special boolean function that can be used to return different values based on the result of boolean comparisons, similar to `if`/`then`/`else` statements in other languages. 

Instead of a fixed number of parameters, `case(...)` takes a sequence of `boolean, value` pairs. Each pair will be evaluated to see if the boolean condition is true, and if so, the specified value will be returned. If no boolean condition can be evaluated as true, the function will return `null`.

For example, you can use `case(...)` with the CMS Open Payments [General Payment Data](https://openpaymentsdata.cms.gov/dataset/General-Payment-Data-Detailed-Dataset-2014-Reporti/sb72-gakb?) to get a single column for `name` based on whether the payment was for a physician or a teaching hospital: 

{% include tryit.html domain='openpaymentsdata.cms.gov' path='/resource/y4mv-5s9j.json' args="$select=CASE(covered_recipient_type = 'Covered Recipient Teaching Hospital', teaching_hospital_name, covered_recipient_type = 'Covered Recipient Physician', physician_first_name || ' ' || physician_last_name) AS name" %}

The `case(...)` function is also very helpful in aggregations. In this case, we'll count the number of disputed and undisputed payments for each physician in the state of Washington:

{% include tryit.html domain='openpaymentsdata.cms.gov' path='/resource/y4mv-5s9j.json' args="covered_recipient_type=Covered Recipient Physician&$select=physician_profile_id, physician_first_name, physician_last_name, sum(case(dispute_status_for_publication='No', 1, true, 0)) as no_count,sum(case(dispute_status_for_publication='Yes', 1, true, 0)) as yes_count&$group=physician_profile_id, physician_first_name, physician_last_name&recipient_state=WA" %}

Note that in this case we wanted to make sure we returned `0` instead of `null` if we didn't get a match, so the final pair passed into `case(...)` was `true, 0`.


