what it use to be:
Export the fin and sales files from QAD and then it shiws up in comet src????????????????



What we are doing now:

once the fin and sales related files or tables get generated from qad(by dipti)
they are now in the qad>data>comet fin and sales files
dipti then goes to comet
goes to the finance import table page or the sales costsales import table page and does the import
what happens is that there is a batch file that is executed when the import button is clicked
the batch file copies the entire folder content of fin or sales and pastes it in the destination which is cometsrc in ediserver
the batch files are copyfinfiles and copysalesfiles

then after the files get copied to cometsrc they are then imported into the sql tables which will then be reflected on in comet.
