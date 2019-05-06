# SkilletStamper
Quick &amp; Dirty perl-script to fetch Skillet parameters (i.e. what you normnally have in "variables" and "snippets" section)

## Installation
Its a perl script made in perl 5.26 on Ubuntu. You need to use the Spreadsheet::XLSX module e.g. from CPAN

## Usage
```bash
skilletstamper.pl excelfile.( xlsx || xlsm )
```
### Example
```bash
[13:54:30][root@delilah][demo]# ls -lat
total 64
drwxr-xr-x 2 root    root     4096 May  6 13:54 .
drwxr-xr-x 3 penners penners 12288 May  6 13:53 ..
-rwxrwxr-x 1 penners penners  8973 May  6 13:14 skilletstamper.pl
-rwxr-xr-x 1 penners penners 27824 May  6 13:05 playsheet2.xlsm
-rwxrwxrwx 1 penners penners   178 May  5 17:44 S5Objects_stamp.xml
-rwxrwxrwx 1 penners penners   116 May  5 13:58 ObjectTags_stamp.xml
[13:54:33][root@delilah][demo]# ./skilletstamper.pl playsheet2.xlsm
Parsing sheet: Variable Snippets

Checking column 2 of 10 ... doing XML!

 XML Template : ObjectTags_stamp.xml
 XPATH        : /config/devices/entry[@name='localhost.localdomain']/vsys/entry[@name='vsys1']/tag
 Outputfile   : ObjectTags.060520191354.xml

Checking column 4 of 10 ... doing XML!

 XML Template : S5Objects_stamp.xml
 XPATH        : /config/devices/entry[@name='localhost.localdomain']/vsys/entry[@name='vsys1']/address
 Outputfile   : S5Objects.060520191354.xml

Checking column 6 of 10 ...
Checking column 8 of 10 ...
Checking column 10 of 10 ...
Parsing sheet: Static Snippets


	 Enter a one-word label (will be visible in PanHandler as headline of
	 the box that is representing the Skillet)
	 label [CR]:S5 Rules test


	 Enter a description for the label (will be visible in PanHandler
	 as text inside the box that is representing the Skillet)
	 description [CR]:SkilletStamper auto-generated based on excel file: Some arbitrary rules to demo


	 Enter a name for the collection (can have blankspace, will be visible in
	 PanHandler as name for the Skillet Collection.) It is not stupid to use 
	 the same name for Skillet Collection and Repository.
	 skillet collection [CR]:StampedSkillets

[13:56:33][root@delilah][demo]# ls -lat
total 76
drwxr-xr-x 2 root    root     4096 May  6 13:56 .
-rw-r--r-- 1 root    root     2242 May  6 13:56 .meta-cnc.yaml
-rw-r--r-- 1 root    root      306 May  6 13:54 ObjectTags.060520191354.xml
-rw-r--r-- 1 root    root      551 May  6 13:54 S5Objects.060520191354.xml
drwxr-xr-x 3 penners penners 12288 May  6 13:53 ..
-rwxrwxr-x 1 penners penners  8973 May  6 13:14 skilletstamper.pl
-rwxr-xr-x 1 penners penners 27824 May  6 13:05 playsheet2.xlsm
-rwxrwxrwx 1 penners penners   178 May  5 17:44 S5Objects_stamp.xml
-rwxrwxrwx 1 penners penners   116 May  5 13:58 ObjectTags_stamp.xml
[13:56:40][root@delilah][demo]# 
```

You see in the example:
- The Variable Snippets tab of your excel file must already contain the {{variables}} from which PanHandler will create the objects. Instead of filling a quickly-becoming-complex variable-section in jinja: You put every instance of an Object, Rule or other (repeating) definition into the excel and keep better overview. 
- the excel has a format in which you must keep the structure of lines and columns. See the example and add more double-columns on-top (or instead of) existing ObjectTags and S5Objects examples.
- As you add {{jinja}} variables into the excel you prepare in parallel for each double-column an XML file that has only one instance stanza of the object(s) you want to create. It should be a something_stamp.xml file in the same directory. The tool will replicate this template stanza and with each replication replace the {{jinja}} occurrences defined in the excel with the value given in the second column of this object type. 
- In a second sheet you have Static Skillets that are just existing XML Files you can add. You would do the same in the skillets section of a meta-cnc file.
- Last the tool creates the meta-cnc and the XML snippets with replicated Objects and values (hardcoded now in XML for Panhandler). You may leave {{jinja}} variables untouched as you set them in the excel or not, or you can fill existing skillet-yaml snippets.

## the Excel sheet
You can overwrite the examples and add new examples of the same structure. Outside (e.g. leftmost column) you can insert comments or group objects. Structure means
- in Variable Skillets sheet: Object Types are organised in double-columns. Each double-column has {{variablename}} and Value in left and right column respectively. The rowblock below each double-column has instances of the same object type that is represented by the XPath in the heading lines.
Heading lines further have a yes/no to set if the cells in row-block below will be processed. ANd an XML stanza file with one instance of the Object
- in Static Skillets sheet: just a single block with instances of skillet files that will be added complete as they are into the skillets section of the yaml file
- in both sheets one instance in a line-block is separated by the next with a single empty line. Do not use empty lines for anything besides separating 2 instances of an object. Do not insert entire lines into the sheet either ..
## Do not
use excessive extra-characters or make a mess of special characters in the excel cells. This is all quick&dirty and not very failsafe. 
