# PyPeaks Dataframe
I am PyPeaks, a library and framework for extracting, transforming, and loading (ETL) data. The first app I built is called “Preview File”, which can check if a file is a comma-separated values (CSV) file and identify its delimiter (other than a comma). If the file passes validation, you can preview a billion-row file instantly. The file can be divided into 1,000 or more partitions to extract and validate the first row of every partition. The whole process of this app for many cases I test runs instantly, no matter if the file size is over 10GB or has billions of rows.

## The Folder "preview-file" is Python Call Rust 

This app is written in Rust with Python binding using Pyo3. See preview-file.py for instruction.

The following Peaks.py does not has rust code, however it use a C++ file seek function, it can run very fast.

## The File "preview.py" is Python without Call Rust
This app is uploaded in this repository and used to validate and preview CSV files. For every 1% position of a CSV file, it will extract one row for validation and preview. On the screen, it will display 20 rows but will output all validated rows to a disk file. If you have any issues with this app, please leave your message at    https://github.com/hkpeaks/pypeaks/issues. 

The app will gradually expand to become an ETL Framework for Polars (and/or Pandas, DuckDB, NumPy) with the implementation of the newly designed SQL statements. The ETL script will be compatible with the Peaks Consolidation https://github.com/hkpeaks/peaks-consolidation, meaning you can run the script using Python with Polars or purely using the Peaks runtime without Python.
    
    How to use this app:

    Python Peaks.py File or File Path

    e.g. python peaks data.csv

         python peaks "d:\your folder\data.csv"

## New Query Statement for File, In-memory Table and Network Stream

Note: Use of "." to indicate it is member of your defined function is optional. 
First line is to define data extraction and data load. Below are 3 possible scenarios:-

UserDefineFunctionName = from Extraction to Load

Or 

UserDefineFunctionName = from Extraction, Extraction, Extraction to Load

Or

UserDefineFunctionName = from Extraction to Load, Load, Load

You can define query/ data transformation function from second line and after.

Examples:

#### ExpandFile = from Fact.csv to 1BillionRows.csv

.ExpandFactor: 123

#### JoinTable = from 1BillionRows.csv to Test1Results.csv

.Filter: Saleman(Mary,Peter,John)

.JoinTable: Product, Category => InnerJoin(Master.csv)

.AddColumn: Quantity, Unit_Price => Multiply(Amount)

.Filter: Amount(Float20000..29999)

.GroupBy: Saleman, Shop, Product => Count() Sum(Quantity) Sum(Amount)

.OrderBy: Saleman(A) Product(A) Date(D)

#### SplitFile = from Test1Results.csv to FolderLake

.CreateFolderLake: Shop

#### FilterFolder = from Outbox/FolderLake/S15/*.csv to Result-FilterFolderLake.csv

.Filter: Product(222..888) Style(=F)

#### ReadSample2View = from Outbox/Result-FilterFolderLake.csv to SampleTable

.ReadSample: StartPosition%(0) ByteLength(100000)

.View
## Command List

   AddColumn{Column, Column => Math(NewColName)} 
   
        where Math includes Add, Subtract, Multiply & Divide
    
   BuildKeyValue{Column, Column ~ KeyValueTableName}
   
   CurrentSetting{StreamMB(Number) Thread(Number)}
  
   Distinct{Column, Column}
 
   Filter{Column(CompareOperator Value) Column(CompareOperator Value)}
 
   FilterUnmatch{Column(CompareOperator Value) Column(CompareOperator Value)}

        where Compare operator includes >,<,>=,<=,=,!= & Range e.g. 100..200
              Compare integer or float e.g. Float > Number, Float100..200
   
   GroupBy{Column, Column => Count() Sum(Column) Max(Column) Min(Column)}
   
   JoinKeyValue{Column, Column => JoinType(KeyValueTableName)} 
        
        where JoinType includes AllMatch, Filter & FilterUnmatch
   
   JoinTable{Column, Column => JoinType(KeyValueTableName)}

        where JoinType includes AllMatch & InnerJoin
   
   OrderBy{PrimaryCol(Sorting Order) SecondaryCol(Sorting Order)}       
  
   OrderBy{SecondaryCol(Sorting Order) => CreateFolderLake(PrimaryCol) ~ FolderName or FileName.csv}

        where Sorting Order represents by A or D, to sort real numbers, use either FloatA or FloatD
   
   ReadSample{StartPosition%(Number) ByteLength(Number)}
   
   ReadSample{Repeat(Number) ByteLength(Number)}   
   
   Select{Column, Column}
   
   SelectUnmatch{Column, Column}
   
   SplitFile{FileName.csv ~ NumberOfSplit}
   
   CreateFolderLake{Column, Column ~ SplitFolderName}
   
   View{TableName}
