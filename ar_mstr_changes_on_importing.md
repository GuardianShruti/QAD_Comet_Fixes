* there was an issue where a bunch of ar_paid_dates showed up empty in comet
* issue was that all the old data before 9/9/2025, the ar_paid_date showed up as blank even if it was closed and there was a paid amount even
* since everything was fine post live data for the pre live data we want to keep the previous info and not loose it and only import correct data
* so ujwal first ran the import for data before live date and made sure it was correct
* then when we do a combo of current data we want to make sure that the faulty data isnt being added or rewritten to get rid of the paid date and amount
* so we changed the import mpt finance stored procedure for ar_mstr so that we dont delete that data and dont import data like it too.

* to keep it simple we dont care whether it was opened or closed invoice
* we only care that there should be a paid date if even a penny is paid so we care about 1)applied and 2)paid_date
