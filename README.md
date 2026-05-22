📘 Access Database Generator (ADOX + OleDb + CSV Import)
A complete automation workflow for generating Access DB files from CSV datasets.
---
🚀 Project Overview
This project demonstrates how to automatically generate an Access Database (.accdb) using ADOX, then import CSV data using OleDbDataAdapter.
It is designed for developers who need to:

Convert CSV datasets into Access DB

Automate database creation

Build ETL pipelines

Work with legacy Access systems

Perform offline data processing
---
🧩 Key Features
✔ Create .accdb files programmatically

✔ Define table schema using ADOX

✔ Insert data using OleDb

✔ Supports Unicode text (WChar)

✔ Works with any CSV dataset

✔ No manual Access work required
---
📥 Requirements
Interop.ADOX.dll (add as reference)

Microsoft ACE OLEDB 16.0 provider

CSV dataset (example from Kaggle)

.NET Framework / .NET Core (Windows only)
---
📊 Sample Dataset (Kaggle)
Employee Sample Data:
https://www.kaggle.com/datasets/williamlucas0/employee-sample-data?select=Employee+Sample+Data+1.csv
---
🏗️ Architecture Diagram
Code
+------------------+        +----------------------+        +----------------------+
|   CSV File       | -----> |   DataTable Loader   | -----> |   Access DB Creator  |
| (Kaggle Dataset) |        | (Parse CSV to DT)    |        | (ADOX .accdb schema) |
+------------------+        +----------------------+        +----------------------+
                                                                  |
                                                                  v
                                                         +----------------------+
                                                         |  OleDbDataAdapter   |
                                                         |  Insert into table  |
                                                         +----------------------+
                                                                  |
                                                                  v
                                                         +----------------------+
                                                         |   EmployeeDB.accdb   |
                                                         +----------------------+




---
🔄 CSV → Access DB Flowchart
Code
 ┌──────────────────────┐
 │   Load CSV File      │
 └──────────┬───────────┘
            │
            v
 ┌──────────────────────┐
 │ Convert to DataTable │
 └──────────┬───────────┘
            │
            v
 ┌──────────────────────┐
 │ Create .accdb (ADOX) │
 └──────────┬───────────┘
            │
            v
 ┌────────────────────────────┐
 │ Create Table Schema (ADOX) │
 └──────────┬─────────────────┘
            │
            v
 ┌────────────────────────────┐
 │ Insert Rows (OleDbAdapter) │
 └──────────┬─────────────────┘
            │
            v
 ┌──────────────────────┐
 │   Access DB Ready    │
 └──────────────────────┘



---
🛠️ Sample Code — Create Access DB (ADOX)


  if (!File.Exists(accesspath))
                {
                    ADOX.Catalog cat = new Catalog();
                    cat.Create(Convert.ToString("Provider=Microsoft.ACE.OLEDB.16.0;Data Source=") + accesspath);
                    ADOX.Table table = new Table();

                    table.Name = Path.GetFileNameWithoutExtension(accesspath);

                    if (lstCols.Count > 0)
                    {
                        lstCols.ForEach(c =>
                        {
                            ADOX.Column col = new Column();
                            col.Name = c;
                            col.Type = ADOX.DataTypeEnum.adVarWChar;
                            col.Attributes = ADOX.ColumnAttributesEnum.adColNullable;
                            table.Columns.Append(col);
                        });
                    }

                    cat.Tables.Append(table);
                    cat = null;
                    flg = true;
                }


---
🔌 Sample Code — Insert CSV Data (OleDbDataAdapter)

  using (OleDbConnection conn = new OleDbConnection(Convert.ToString("Provider=Microsoft.ACE.OLEDB.16.0;Data Source=") + accesspath))
                {
                    using (OleDbDataAdapter dbadpter = new OleDbDataAdapter("SELECT * FROM " + accessfilename + "", conn))
                    {
                        dbadpter.InsertCommand = new OleDbCommand("Insert Into " + accessfilename + " ( [Employee_ID],[Full_Name],[Job_Title]"
                            + ",[Department],[Gender],[Hire_Date] ) Values (@Employee_ID, @Full_Name, @Job_Title, @Department, @Gender, @Hire_Date)");

                        lstCols.ForEach(x =>
                        {
                            dbadpter.InsertCommand.Parameters.Add("@" + x, OleDbType.WChar, 255).SourceColumn = x;
                        });

                        //dbadpter.InsertCommand.Parameters.Add ("@Employee_ID",  OleDbType.WChar , 255, "Employee_ID");


                        dbadpter.InsertCommand.Connection = conn;
                        dbadpter.InsertCommand.Connection.Open();
                        dbadpter.Update(dtemp);
                        dbadpter.InsertCommand.Connection.Close();
                    }
                }


---
📌 Notes
ADOX is used only for database + table creation

OleDb handles data insertion

Column names must match exactly

Works with .accdb (ACE 16.0 provider)

Fully automates CSV → Access conversion

