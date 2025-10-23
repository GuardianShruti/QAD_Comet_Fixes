* there was an issue where a bunch of ar_paid_dates showed up empty in comet
* issue was that all the old data before 9/9/2025, the ar_paid_date showed up as blank even if it was closed and there was a paid amount even
* since everything was fine post live data for the pre live data we want to keep the previous info and not loose it and only import correct data
* so ujwal first ran the import for data before live date and made sure it was correct
* then when we do a combo of current data we want to make sure that the faulty data isnt being added or rewritten to get rid of the paid date and amount
* so we changed the import mpt finance stored procedure for ar_mstr so that we dont delete that data and dont import data like it too.

* to keep it simple we dont care whether it was opened or closed invoice
* we only care that there should be a paid date if even a penny is paid so we care about 1)applied and 2)paid_date



*----------------------------------the changed code for the stored procedure for the live code------------------------------------*
	if(@iar_mstr = 'true') begin
	/* DELETE ar_mstr WHERE ar_inv_nbr in(
		SELECT ar_inv_nbr FROM OPENROWSET('Microsoft.Jet.OLEDB.4.0',
		'Excel 8.0;Database=C:\WebApplications\CometSrc\Fin\ar_mstr.xls;IMEX=1', 
		'SELECT ar_inv_nbr FROM [Sheet1$]')); */
	/*DELETE ar_mstr WHERE ar_inv_nbr in(
		SELECT ar_inv_nbr FROM OPENROWSET('MSDASQL',
		'Driver={Microsoft Access Text Driver (*.txt, *.csv)}', 
		'SELECT ar_inv_nbr FROM C:\WebApplications\CometSrc\Fin\ar_mstr.csv')); */
		--shruti 09/25/2025 - we have moved to the new QAD and caused issue when looking at old data
		DELETE ar_mstr WHERE ar_inv_nbr in(
		SELECT ar_inv_nbr FROM OPENROWSET('Microsoft.ACE.OLEDB.12.0',
		'TEXT;Database=C:\WebApplications\CometSrc\Fin;HDR=1', 
		'SELECT ar_inv_nbr FROM ar_mstr.CSV where not(ar_applied>0 and ar_paid_date IS NULL)'));

	/*Insert into ar_mstr select * FROM OPENROWSET('Microsoft.Jet.OLEDB.4.0',
		'Excel 8.0;Database=C:\WebApplications\CometSrc\Fin\ar_mstr.xls;IMEX=1', 'SELECT * FROM [Sheet1$]') */

	/*Insert into ar_mstr select * FROM OPENROWSET('MSDASQL',
		'Driver={Microsoft Access Text Driver (*.txt, *.csv)}', 
		'SELECT * FROM C:\WebApplications\CometSrc\Fin\ar_mstr.csv') */
		--shruti 09/25/2025 - we have moved to the new QAD and caused issue when looking at old data
		Insert into ar_mstr select * FROM OPENROWSET('Microsoft.ACE.OLEDB.12.0',
		'TEXT;Database=C:\WebApplications\CometSrc\Fin;HDR=1', 'SELECT * FROM ar_mstr.CSV where not(ar_applied>0 and ar_paid_date IS NULL)')
	end


  *------------------------------there was an issue with prepaid amount----------------------*

  * for the prepaid amounts when we use it to pay off stuff the paid date would not show up
  * example is if a company gives us 75K in advance to spend and we now want to charge them 2K for shipping we take that amount from the prepaid amount
  * but when this whole process takes place in open item adjustment paid date doesnt get used so because of this the logic breaks
  * to recitify this we are going to use make sure that any closed amounts dont get deleted as they are done and dusted and we dont care for the paid date here
 
