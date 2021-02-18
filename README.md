Ninja Skills Writeup:
	
I SSH'd into the machine using the following command:

	ssh new-user@{MACHINE_IP}; 
	(password: new-user)
	
At the beginning of the ctf, it has the following instructions:

Answer the questions about the following files:

	8V2L 
	bny0 
	c4ZX 
	D8B3 
	FHl1 
	oiMO 
	PFbD 
	rmfX 
	SRSq 
	uqyw 
	v2Vb 
	X1Uy 

The aim is to answer the questions as efficiently as possible.
	
So the first thing I did was find exactly where all these files were. I copied the above list of filenames and saved them to a file called: 

	'filelist.txt'

I then created a shell script to find the file locations, and then list those locations into a file called: 'filelocations.txt' Here is the code below:
	
	cat filelist.txt | \
	while read FILENAME
	do
  	find / -name "$FILENAME" -type f 2>/dev/null >> filelocations.txt;
	done
	
Here are the results:

	/etc/8V2L
	/mnt/c4ZX
	/mnt/D8B3
	/var/FHl1
	/opt/oiMO
	/opt/PFbD
	/media/rmfX
	/etc/ssh/SRSq
	/var/log/uqyw
	/home/v2Vb
	/X1Uy
	
Now that the set-up has been done, let's get to these questions:
	

Question #1: Which of the above files are owned by the best-group group(enter the answer separated by spaces in alphabetical order)

For this one, I created the following script:

		{findgroupscode.png}
	
This script scans through the file locations, and performs the 'scan' command on each of the files. Then, for each of the files, it outputs the following:

	"filename= {FILE NAME}, groupname= {GROUP NAME}"
	
Here is the resultant output:
		
		{findgroupsresults}
		
After looking at the results, we see the following files in the "best-group" group:
		
		D8B3, v2Vb
		
Question #2: Which of these files contain an IP address?

For this question, I banged together a (admitidly ugly) script:

	{ipfindcode}
	
Basically, the script cycles through the file locations that we determined before, and for each of the files, it greps for the contained regex pattern that looks for IP addresses. After that, it echos the filename.

The script returned the following results:

	/etc/8V2L
	/mnt/c4ZX
	/mnt/D8B3
	/var/FHl1
	1.1.1.1
	/opt/oiMO
	/opt/PFbD
	/media/rmfX
	/etc/ssh/SRSq
	/var/log/uqyw
	/home/v2Vb
	/X1Uy

So, we see a list interupted by 1 IP address (1.1.1.1). Due to the way the script is written, it attempts a grep on the filename, and then, whether it finds anything or not, it then displays the filename. Since the IP address is found before the "oiMO" file, that is our answer.

Question #3: 

Which file has the SHA1 hash of 9d54da7584015647ba052173b84d45e8007eba94

For this one, I ran the follwing script:

	cat filelocations.txt | \
	while read FILENAME

	do
	sha1sum "$FILENAME";

	done

Received the following output:

	0323e62f06b29ddbbe18f30a89cc123ae479a346  /etc/8V2L
	9d54da7584015647ba052173b84d45e8007eba94  /mnt/c4ZX
	2c8de970ff0701c8fd6c55db8a5315e5615a9575  /mnt/D8B3
	d5a35473a856ea30bfec5bf67b8b6e1fe96475b3  /var/FHl1
	5b34294b3caa59c1006854fa0901352bf6476a8c  /opt/oiMO
	256933c34f1b42522298282ce5df3642be9a2dc9  /opt/PFbD
	4ef4c2df08bc60139c29e222f537b6bea7e4d6fa  /media/rmfX
	acbbbce6c56feb7e351f866b806427403b7b103d  /etc/ssh/SRSq
	57226b5f4f1d5ca128f606581d7ca9bd6c45ca13  /var/log/uqyw
	7324353e3cd047b8150e0c95edf12e28be7c55d3  /home/v2Vb
	59840c46fb64a4faeabb37da0744a46967d87e57  /X1Uy

Therefore, c4ZX is our answer.



Question #4: Which file contains 230 lines?

Used the following script:

	cat filelocations.txt | \
	while read FILENAME

	do
	wc "$FILENAME";

	done

Returned the following:

	209   209 13545 /etc/8V2L
	209   209 13545 /mnt/c4ZX
	209   209 13545 /mnt/D8B3
	209   209 13545 /var/FHl1
	209   209 13545 /opt/oiMO
	209   209 13545 /opt/PFbD
	209   209 13545 /media/rmfX
	209   209 13545 /etc/ssh/SRSq
	209   209 13545 /var/log/uqyw
	209   209 13545 /home/v2Vb
	209   209 13545 /X1Uy

According to the output, all the files here have exactly 209 lines. I then noticed that one of the files on the original list was not found in my original script: "bny0". So, by elimination, I inferred that it was the correct file.

Question #5: 

Which file's owner has an ID of 502?

Ran the following script:

	cat filelocations.txt | \
	while read FILENAME

	do
	ls -la "$FILENAME";

	done

Got this output:
	
	{listoffileusers.png}
	
The files all have "new-user" as the owner. Except for one: "X1Uy". It has "newer-user" as it's owner.

So I typed the following:

	id -u newer-user

Returned the following:

	502
	
Therefore, "X1Uy" is our answer.


Question #6: Which file is executable by everyone?

Based on the previous output:

	{listoffileusers.png}
	
File "8V2L" is executable by everyone.


And that completes this room! Thank you for reading!
