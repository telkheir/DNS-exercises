<h1>DNS Exercises: Local Cache, A-Records, and CNAMES</h1>

The purpose of this tutorial is to gain an understanding of some of the workings of DNS through concepts like A-records and CNAME records. We will also explore our local DNS cache and root hints. This project was a follow up to the <a href = "https://github.com/telkheir/implementing-active-directory">implementing Active Directory in Azure</a> tutorial.


<h2>Environments and Technologies Used</h2>
    <ul>
      <li>Microsoft Azure</li>
      <li>Virtual Machines (Azure)</li>
      <li>Remote Desktop</li>
      <li>Microsoft Active Directory</li>
    </ul>

<h2>Operating Systems Used</h2>
    <ul>
      <li>Windows 10</li>
    </ul>

<h2>Navigation</h2>
    <ol>
      <li><a href = "#step_1">A-Record Exercise</a></li>
      <li><a href = "#step_2">Local DNS Cache Exercise</a></li>
      <li><a href = "#step_3">CNAME Record Exercise</a></li>
    </ol>

<h2>Tutorial</h2>
    <ol>
      <li><h3 id = "step_1">A-Record Exercise</h3>
          First we're going to use Remote Desktop to access both DC-1 and Client-1 as an admin. In my example, I logged in as tekdomain.com\tek_admin. We're going to try to ping 'mainframe' from the Client-1 VM. The Client-1 VM will first search the local cache for 'mainframe' but will not come up with anything. It will then search the host file, where it again should fail to find the domain name, before checking the DNS last. This ping should fail because 'mainframe' does not exist in our domain's records yet.
          <br>
          Using the 'nslookup' command will also fail because there is no DNS record.
          <br><br>
          <img width="656" alt="dns-cmd-mainframe-fail" src="https://github.com/telkheir/DNS-exercises/assets/145223639/228328ce-5b40-4454-a58f-e8a94c639e5f">
          <br><br>
          Now we are going to switch to the DC-1 VM to register 'mainframe' as a domain name in our device and any other that may log into our domain. To do so, go to the Server Manager on DC-1 and in the top right corner, click on Tools to see a drop-down menu and select DNS. Click "DC-1", then "Forward Lookup Zones", and then click on your domain name to view a list of all of the device's A-records.
      <br><br>
          
https://github.com/telkheir/DNS-exercises/assets/145223639/97712cc9-9423-4433-b544-c78d74a0c05e     

<blockquote>
           An A-record is a DNS record used to map a hostname to its respective IPv4 address.<br>
          Forward Lookup Zones refer to hostname to IPv4 mapping<br>
          Reverse Lookup Zones refer to IPv4 to hostname mapping
      </blockquote>
      <br>
      Right click on your domain name and select New Host (A or AAAA). Name it 'Mainframe' and set the IP address to that of the DC-1 VM. 'Mainframe' should now be added to our domain's list of A-records. If we try to ping 'mainframe' from Client-1 once again, it will send data packets to the DC-1 VM.
          <br><br>
          [image - adding new arecord and/or pinging mainframe successfully]
          <br><br>
      </li>
      <li><h3 id = "step_2">Lcal DNS Cache Exercise</h3>
          Return to the DC-1 VM's list of A-Records and change the 'mainframe' IP address to 8.8.8.8. Back in the Client-1 VM, try to ping 'mainframe' once again. You should recieve information back from the old address (DC-1) because the data still exists on the local cache of Client-1. As previously mentioned, when looking up an address, the computer will first check the local cache, followed by the host file, and lastly the DNS. Since this DNS record exists in the local cache of Client-1, the computer will not continue on to check the host file or DNS for the updated IPv4 address of 'Mainframe'.
          <br><br>
          [image - changed a-record and client 1 cmd ping for old address]
          <br><br>
          We can observe our device's local DNS cache by typing "ipconfig/displaydns" into the command line.
          <br><br>
          [image - cmd local dns cache]
          <br><br>
          We need to clear the local cache of Client-1 so that it will check the DNS for the updated IP address of 'Mainframe'. To do so, type "ipconfig/flushdns" into the command line. You may need to have the command prompt open as an administrator.
          <br><br>
          [image - cmd flush local cache]
          <br><br>
          Observe the local DNS cache once again by typing "ipconfig/displaydns" into the command line and try to ping "mainframe" once again. The new address (8.8.8.8) should be used this time since there is no longer any record for 'mainframe' in our local cache for our device to default to.
          <br><br>
          [image - cmd, empty dns cache and pinging new address]
      </li>
      <li><h3 id = "step_3">CNAME Record Exercise</h3>
          A CNAME is a record that maps one domain name to another. In this last exercise, we will create a CNAME record that points any device in our domain to Google.com when we try to ping the word 'search'. First, let's test out what happens when we type 'ping search' in the command line of the Client-1 VM.
          <br><br>
          [image - failed ping of search cmd line]
          <br><br>
          We can see the ping fail because there is no hostname 'search' in our local cache, host file, or DNS. To set up this record, go to DC-1, right click on your domain name, and click "New Alias (CNAME)".
          <br><br>
          [image - highlight the proper links]
          <br><br>
          Set the name to 'search' and put Google down as the domain name. Return to the Client-1 device and type "ping search" into the command line again. Our newly made CNAME record should redirect the data packets to Google.com.
          <br><br>
          [image - successful ping]
      </li>
    </ol>
