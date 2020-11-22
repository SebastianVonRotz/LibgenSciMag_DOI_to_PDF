### ToDo
* [ ] Define Code envrionment
* [ ] Create DB interface to get id from DOI
* [ ] Get torrent file for each id
* [ ] Extract the file number for each specific torrent file
* [ ] Create a loop which does the following:
	* Download specific torrent file with file number
	* Extract pdf file into other dir
	* Delete the specific file and delete adjacent torrents

### General Steps
1. Get DOI List from Matlab
2. Use DOI and local Libgen Ref DB to get id
3. Get the torrent by rounding down the id
4. Use aria2c to download the specific file from the torrent
5. Extract the pdf file from the downloaded files
6. delete the other files

### Detailed Example
###### Get DOI List from Matlab
Available as .txt file with upto 38'000 lines
Also available as 1000 line size files

###### Use DOI and local Libgen Ref DB to get id
Check if DOI is in DB, then return ID

	MariaDB [scimag]> select doi, id, md5 from scimag where doi = '10.1136/bmj.b5066';
	+-------------------+----------+----------------------------------+
	| doi               | id       | md5                              |
	+-------------------+----------+----------------------------------+
	| 10.1136/bmj.b5066 | 44768452 | 3f70b73b59fbdd049b94f84bcf7f7933 |
	+-------------------+----------+----------------------------------+


###### Get the torrent by rounding down the id
With the id the torrent number can be calculated
Download torrent from http://gen.lib.rus.ec/scimag/repository_torrent/

	wget http://gen.lib.rus.ec/scimag/repository_torrent/sm_46000000-46099999.torrent
	
###### Use aria2c to download the specific file from the torrent
Use the id further to download the specific file from the torrent itself the specific file is identified also by the id

	aria2c -T sm_44700000-44799999.torrent --show-files | grep libgen.scimag44768000-44768999.zip

This returns the specific line of the file, with which the speicific file can be downloaded

 	69|./44700000/libgen.scimag44768000-44768999.zipzip
 
###### Use aria2c to download the specific file from the torrent
 
Dowload the specific file with:

	aria2c -T sm_44700000-44799999.torrent --select-file 69

According to https://aria2.github.io/manual/en/html/aria2c.html#bittorrent-metalink-options adjaced files also get downloaded

	libgen.scimag44767000-44767999.zip  libgen.scimag44768000-44768999.zip  libgen.scimag44769000-44769999.zip

###### Extract the pdf file from the downloaded files
Extract the specific .zip file

	unzip libgen.scimag44768000-44768999.zip

Resulting in the following directories

	10.1017  10.1130  10.1134  10.1135  10.1136

The example DOI is: 10.1136/bmj.b5066 therefor with the follwing command the file can be accessed with:

	44700000/10.1136/bmj.b5066.pdf
	
