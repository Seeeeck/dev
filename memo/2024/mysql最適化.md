### 複合indexは選択性の低いものを先頭に置いた方がいいらしい　
> Create only the indexes that you need to improve query performance. Indexes are good for retrieval, but slow down insert and update operations. If you access a table mostly by searching on a combination of columns, create a single composite index on them rather than a separate index for each column. The first part of the index should be the column most used. **If you always use many columns when selecting from the table, the first column in the index should be the one with the most duplicates, to obtain better compression of the index**.[†](https://dev.mysql.com/doc/refman/8.0/en/data-size.html)

### 一時テーブルを使用する際、varcharカラムが使用するメモリ配分
**MYSQL 5.7では一時テーブルを使用する場合VARCHARはCHARとして使用される、つまり、カラムに保存されてる実際の最大長さが定義の長さを満たないとしてもメモリ上で全てのカラムに定義の最大長さのメモリ空間が割り振られる。**
> In-memory temporary tables are managed by the MEMORY storage engine, which uses fixed-length row format. VARCHAR and VARBINARY column values are padded to the maximum column length, in effect storing them as CHAR and BINARY columns.[†](https://dev.mysql.com/doc/refman/5.7/en/internal-temporary-tables.html)

**MYSQL 8では TempTable storage engine が使用される場合、一つのvarcharデータは実際の長さの所用するメモリとNULL flag,データの長さ,データポインターを保持するための16byteのメモリが使用される。**
> When in-memory internal temporary tables are managed by the TempTable storage engine, rows that include VARCHAR columns, VARBINARY columns, and other binary large object type columns (supported as of MySQL 8.0.13) are represented in memory by an array of cells, with each cell containing a NULL flag, the data length, and a data pointer. Column values are placed in consecutive order after the array, in a single region of memory, without padding. Each cell in the array uses 16 bytes of storage. The same storage format applies when the TempTable storage engine allocates space from memory-mapped files.

> When in-memory internal temporary tables are managed by the MEMORY storage engine, fixed-length row format is used. VARCHAR and VARBINARY column values are padded to the maximum column length, in effect storing them as CHAR and BINARY columns.[†](https://dev.mysql.com/doc/refman/8.0/en/internal-temporary-tables.html)

**disk一時テーブルを使用する際、varcharカラムには実際長さのデータ空間が使用される。**


### unique indexに接頭辞をつけられる、これにより、過剰に長さを定義したvarcharカラムによるindexの長さを減らせる
> A UNIQUE index creates a constraint such that all values in the index must be distinct. An error occurs if you try to add a new row with a key value that matches an existing row. **If you specify a prefix value for a column in a UNIQUE index, the column values must be unique within the prefix length.** A UNIQUE index permits multiple NULL values for columns that can contain NULL.[†](https://dev.mysql.com/doc/refman/8.0/en/create-index.html)