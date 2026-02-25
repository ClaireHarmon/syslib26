# February 25, 2026
## Belated entry for using yaz to search a library's catalog

To open yaz:
```
yaz-client
```

To search UK's library catalog:
```
Z> open saalck-uky.alma.exlibrisgroup.com:1921/01SAA_UKY
```

List of commands:
```
man yaz-client
```

Example search:
```
Z> find @and @attr 1=4 "information" @attr 1=21 "library science"
```
```find``` is the command that sends a search request
```@and``` is the operator signifying a Boolean AND search of the next two attributes
```@attr 1=4``` instructs the query to search for the term in the Title field
```"information"``` is the term for the Title search
```@attr 1=21``` instructs the query to search for the term in the subject heading field
```"library science"``` is the second search term for the subject heading search

Use ``show``` command to see records:
```
show 1
```
This shows the first record.

You can examine files retrieved after exiting yaz. Exit, then:
```
head records.marc
```
Use ```file``` command to determine type:
```
file records.marc
records.marc: MARC21 Bibliographic
```
Convert file to JSON:
```
yaz-marcdump -o json records.marc > records.json
```
Format JSON file for readability:
```
jq . records.json > records-formatted.json
```

This is just the basics, but there is some more advanced stuff in the lecture text. 

