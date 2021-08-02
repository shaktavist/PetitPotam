KB5005413: Mitigating NTLM Relay Attacks on Active Directory Certificate Services (AD CS)

PetitPotam can potentially be used to attack Windows domain controllers or other Windows servers. PetitPotam is a classic NTLM Relay Attack, and such attacks have been previously documented by Microsoft along with numerous mitigation options to protect customers. For example: Microsoft Security Advisory 974926. 
Microsoft released some steps to mitigate NTLM attacks but did not provide any guidance on block PetitPotam.
https://support.microsoft.com/en-us/topic/kb5005413-mitigating-ntlm-relay-attacks-on-active-directory-certificate-services-ad-cs-3612b773-4043-4aa9-b23d-b87910cd3429
Following the submission by Microsoft, Craig Kirby and Benjamin Delpy shared instructions on how to effectively block the unauthenticated PetitPotam attack vector.
https://www.bleepingcomputer.com/news/microsoft/windows-petitpotam-attacks-can-be-blocked-using-new-method/

The below are some instructions to create an executable that can be deployed via GPO.

1. Create Text file
Copy the contents into a file called 'block_efsr.txt' and save it on a location of your choice. I chose to save it in a newly created directory C:\EFSR\
rpc filter add rule layer=um actiontype=block add condition field=if_uuid matchtype=equal data=c681d488-d850-11d0-8c52-00c04fd90f7e add filter add rule layer=um actiontype=block add condition field=if_uuid matchtype=equal data=df1941c5-fe89-4e79-bf10-463657acf44d add filter quit


2. Create Batch File
Open Notepad and add the following line

netsh -f c:\EFSR\block_efsr.txt

Save the file a batch file. I saved it as xyz.bat under the directory C:\EFSR\ but can save under one of your choice.


3. Create Executable
 This will walk you through how to convert your script to an .EXE
1.	Launch iexpress as Administrator (type iexpress in widows search bar and runs as Administrator)
2.	On the welcome screen keep the default selected "Create new Self Extraction Directive File" click Next
3.	Under Package purpose, select "Extract files and run installation command"
4.	Name your package. This can be anything I chose to keep it "block_efsr"
5.	Under Confirmation prompt, select "No prompt"
6.	Under License agreement, select "Do not display a license"
7.	Now for your Packaged files, you will want to select your batch file and your text file. 
8.	Under Install Program to Launch, you will want to put cmd.exe /c xyz.bat in for the "Install Program". Leave "Post Install Command" set as None.
9.	Under Show window, have it set to "Default"
10.	Under Finished Message, keep "No Message", Click Next
11.	Under Package Name and Options, Browse to the location you want to save the file and give the file a name "block_efsr"  Under options, check "Hide File Extracting Progress Animation from User"
12.	Under Configure Restart, select "No restart"
13.	Under Save Self Extraction keep the default and click Next
14.	Under Create Package, Click Next and then Finish

4. Check Filter
1.	Launch cmd prompt as administrator
2.	Run the command netsh rpc filter show filter and verify you don’t have the filters with the condition value mentioned in the text file. Most likely you will only see the message Listing all RPC Filters
3.	Go to your executable file that you created and run it as administrator
4.	Go back to command prompt and run the command netsh rpc filter show filter
5.	You should now see the 2 new filters added
To remove the filters you can just use the command netsh rpc filter delete filter filterkey=[key]
Example netsh rpc filter delete filter filterkey=b78c02cc-f3bb-11eb-b8d4-14f6d8e88018

Now that you have verified it works you can deploy via GPO.
