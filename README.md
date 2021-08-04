<p><strong>KB5005413: Mitigating NTLM Relay Attacks on Active Directory Certificate Services (AD CS)</strong></p>
<p>PetitPotam can potentially be used to attack Windows domain controllers or other Windows servers. PetitPotam is a classic NTLM Relay Attack, and such attacks have been previously documented by Microsoft along with numerous mitigation options to protect customers.&nbsp;For&nbsp;example:&nbsp;<a href="https://docs.microsoft.com/security-updates/SecurityAdvisories/2009/974926">Microsoft Security Advisory 974926</a>.&nbsp;</p>
<p>Microsoft released some steps to mitigate NTLM attacks but did not provide any guidance on block PetitPotam.</p>
<p><a href="https://support.microsoft.com/en-us/topic/kb5005413-mitigating-ntlm-relay-attacks-on-active-directory-certificate-services-ad-cs-3612b773-4043-4aa9-b23d-b87910cd3429">https://support.microsoft.com/en-us/topic/kb5005413-mitigating-ntlm-relay-attacks-on-active-directory-certificate-services-ad-cs-3612b773-4043-4aa9-b23d-b87910cd3429</a></p>
<p>Following the submission by Microsoft, Craig Kirby and Benjamin Delpy shared instructions on how to effectively block the unauthenticated PetitPotam attack vector.</p>
<p><a href="https://www.bleepingcomputer.com/news/microsoft/windows-petitpotam-attacks-can-be-blocked-using-new-method/">https://www.bleepingcomputer.com/news/microsoft/windows-petitpotam-attacks-can-be-blocked-using-new-method/</a></p>
<p>The below are some instructions to create an executable that can be deployed via GPO.</p>
<p><strong><u>1. Create Text file</u></strong></p>
<ul>
<li>Copy&nbsp;the contents into a file called 'block_efsr.txt' and save it on a location of your choice. I chose to save it in a newly created directory named C:\EFSR\</li>
</ul>
<p><strong><em> rpc filter add rule layer=um actiontype=block add condition field=if_uuid matchtype=equal data=c681d488-d850-11d0-8c52-00c04fd90f7e add filter add rule layer=um actiontype=block add condition field=if_uuid matchtype=equal data=df1941c5-fe89-4e79-bf10-463657acf44d add filter quit</em></strong><br /><br /><br /><strong><u>2. Create Batch File</u></strong></p>
<ul>
<li>Open Notepad and add the following line&nbsp;<em><strong>netsh -f c:\EFSR\block_efsr.txt</strong></em></li>
<li>Save it as a batch file. For example, I saved it as xyz.bat under the directory C:\EFSR\ but you may save it under a directory of your choice.</li>
</ul>
<p><strong><u>3. Create Executable</u></strong></p>
<p>In case you were not aware there is an easy way to do this without any 3<sup>rd</sup> party tools using a windows utility called iexpress.</p>
<p>&nbsp;The following steps will walk you through how to convert your script to an&nbsp;<em>.EXE</em></p>
<ul>
<li>Launch <strong>iexpress </strong>as Administrator (type iexpress in windows search bar and runs as Administrator)</li>
<li>On the welcome screen keep the default selected "<em>Create new Self Extraction Directive File</em>" click Next</li>
<li>Under&nbsp;<strong>Package purpose</strong>, select "<em>Extract files and run installation command</em>"</li>
<li>Name your package. This can be anything I chose to keep it "<em>block_efsr"</em></li>
<li>Under&nbsp;<strong>Confirmation prompt</strong>, select "<em>No prompt</em>"</li>
<li>Under&nbsp;<strong>License agreement</strong>, select "<em>Do not display a license</em>"</li>
<li>Now for your&nbsp;<strong>Packaged files</strong>, you will want to select your batch file and your text file.&nbsp;</li>
<li>Under&nbsp;<strong>Install Program to Launch</strong>, you will want to put&nbsp;cmd.exe /c xyz.bat&nbsp;in for the "<em>Install Program</em>". Leave "<em>Post Install Command</em>" set as&nbsp;<em>None</em>.</li>
<li>Under&nbsp;<strong>Show window</strong>, have it set to "<em>Default</em>"</li>
<li>Under&nbsp;<strong>Finished Message,&nbsp;</strong>keep "<em>No Message",&nbsp;</em>Click Next</li>
<li>Under&nbsp;<strong>Package Name and Options</strong>, Browse to the location you want to save the file and give the file a name "<em>block_efsr</em>" Under options, check "<em>Hide File Extracting Progress Animation from User</em>"</li>
<li>Under&nbsp;<strong>Configure Restart</strong>, select "<em>No restart</em>"</li>
<li>Under&nbsp;<strong>Save Self Extraction</strong>&nbsp;keep the default and click Next</li>
<li>Under Create Package, Click Next and then Finish</li>
</ul>
<p><strong><u>Check Filter</u></strong></p>
<ul>
<li>Launch cmd prompt as administrator</li>
<li>Run the command <strong><em>netsh rpc filter show filter</em></strong> and verify you don&rsquo;t have the filters with the condition value mentioned in the text file. Most likely you will only see the message <strong><em>Listing all RPC Filters</em></strong></li>
<li>Go to your executable file that you created and run it as administrator</li>
<li>Go back to command prompt and run the command <strong><em>netsh rpc filter show filter</em></strong></li>
<li>You should now see the 2 new filters added</li>
</ul>
<p>To remove the filters you can just use the command <strong><em>netsh rpc filter delete filter filterkey=[key]</em></strong></p>
<p>Example <strong><em>netsh rpc filter delete filter filterkey=b78c02cc-f3bb-11eb-b8d4-14f6d8e88018</em></strong></p>
<p>Now that you have verified it works you can deploy via GPO.
<p>In order to run the executable via GPO you will need to deploy the files .bat file and text file to windows machines to match the location in your script.</p>
<p>Following article shows how you can place the files via GPO.</p>
<p>http://woshub.com/copy-files-on-all-computers-group-policy/</p>
