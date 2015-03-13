convert-eprime
==============

Python code meant to convert E-Prime files to csvs for analysis. In progress.

## Code
- [**index_eprime_files.py**](index_eprime_files.py): Looks through a folder and finds pairs of edat/txt files (successful runs) and all other combinations of the two filetypes. It notes these other files as requiring individual attention. Eventually it will call convert_eprime to automatically convert the text files in pairs.
- [**convert_eprime.py**](convert_eprime.py): A set of three functions to convert E-Prime files into a more manageable csv format. The functions:
  1.  [etext_to_rcsv](convert_eprime.py#L38): Converts exported "E-Prime text" file to reduced csv based on desired column headers. Make sure, when exporting the edat file as "E-Prime text", that Unicode is turned off.
  2.  [text_to_csv](convert_eprime.py#L91): Converts text file produced by successful completion of E-Prime experiment to csv. Output from text_to_csv can be used to deduce information necessary for text_to_rcsv (e.g. columns to merge, columns to rename, etc.). These variables would then be saved in the headers.pickle file.
  3.  [text_to_rcsv](convert_eprime.py#L174): Converts text file produced by successful completion of E-Prime experiment to reduced csv, using information from the variables contained in headers.pickle. Considerably more complex than text_to_csv, but if used properly the output should be indistinguishable from the output of etext_to_rcsv, only without the tedious step of exporting the "E-Prime text" file by hand.
- [**pickle_files.py**](pickle_files.py): Writes out pickle file with a number of dictionaries used by convert_eprime.py. All of your task-specific information goes here.

## Procedure
- If you plan to export your edat files to "E-Prime text" format by hand, then you can simply use etext_to_rcsv to convert the resulting files. You will need to know which columns in the file are necessary for vector creation and result summarization. Also, you will need to know if you want to remove rows with NaNs. If you're unsure, set remnulls for your task to False.
- If you plan to only use the text files outputted by E-Prime, you can use text_to_csv to convert them. The outputted csv files will be very different from the ones created by etext_to_rcsv, depending on how you coded the experiment. Sometimes columns will be split in the text file but merged in the edat, and column headers will often be different between the two. You can, of course, write your vector creation and result summarization code to work with these csv files instead of the reduced ones created by the other functions.
- If you're used to converting your edat files by hand and all of your code is geared toward that, but you hate it and want to stop without rewriting all of your code, you can use text_to_rcsv. You will need to run text_to_csv at least once per task to determine which columns need to be merged, which ones need to be renamed, etc. to match the format of the "E-Prime text" files you normally deal with. However, once you have that done and the dictionaries in pickle_files.py are set for your task, it should run smoothly for the future. If this interests you, here is (I hope) a fairly generic step-by-step procedure for figuring out those dictionaries:
    1. Using whatever scripts you normally use to create your vectors, identify necessary columns in the edat file.
    2. Add your task to headers (with your edat headers), merge_cols (empty list), merge_col_names (empty list), null_cols (any column), and replace_dict (copy from another task) in pickle_files.py.
    3. Run text_to_csv. It will generate a csv file that will be based on the text file automatically generated by E-Prime.
    4. Look through the csv file and compare with the edat.
      1. There will be some columns with different names that contain the same information (replace_dict).
        * You might also come across a column in the text file that has the same name as an important column in the edat, but with different information. You can rename that column to something else using replace_dict, so that you’re essentially switching out a bad header with a good one (assuming you’ve found the differently-named but corresponding text file header for that edat header).
      2. There will also be some columns that have NULLs right where you need them (on the rows you want to remove), so use one of those for null_cols. You can use more than one if no one of them covers everything.
      3. There may also be columns in the edat that correspond to multiple columns in the csv file created by text_to_csv. For example, one column in the text file might correspond to block 1, with another corresponding to block 2, while in the edat they’re just one column. You can add those columns as lists to merge_cols, with the name of the edat column in the corresponding position in merge_col_names. 
    5. Test it out. Yes, I know this procedure is tedious, but now text_to_rcsv should work with your task. 
