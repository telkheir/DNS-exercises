<h1>DNS Exercises: Local Cache, A-Records, and CNAMES</h1>

The purpose of this tutorial is to gain an understanding of some of the workings of DNS through concepts like A-records and CNAME records. We will also explore our local DNS cache and root hints. This project was a follow up to the <a href = "https://github.com/telkheir/implementing-active-directory">implementing Active Directory in Azure</a> tutorial.
<br>


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
          Now we are going to switch to the DC-1 VM to register 'mainframe' as a domain name in our device and any other that may log into our domain. To do so, go to the Server Manager on DC-1 and in the top right corner, click on Tools to see a drop-down menu and select DNS. Click "DC-1", then "Forward Lookup Zones", and then click on your domain name to view a list of all of the device's A-records. There should already be an A-record for the Client-1 and DC-1 devices.
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
          <img width="567" alt="dns_manager" src="https://github.com/telkheir/DNS-exercises/assets/145223639/c2429be3-5764-46ec-b9b4-ac524ce749c4">
          <img width="693" alt="dns-cmd-successful-mainframe-ping" src="https://github.com/telkheir/DNS-exercises/assets/145223639/8b2f48bd-2ff2-4446-80b5-6b76b3927bd5">
          <br><br>
      </li>
      <li><h3 id = "step_2">Local DNS Cache Exercise</h3>
          Return to the DC-1 VM's list of A-Records and change the 'mainframe' IP address to 8.8.8.8. This can be done by right-clicking the A-record and selecting Properties. If we return to the Client-1 VM and try to ping 'mainframe' once again, we will recieve information back from the old address (DC-1) because the data still exists on the local cache of Client-1. As previously mentioned, when looking up an address, the computer will first check the local cache, followed by the host file, and lastly the DNS. Since this DNS record exists in the local cache of Client-1, the computer will not continue on to check the host file or DNS for the updated IPv4 address of 'Mainframe'.
          <br><br>
          <img width="816" alt="dns-mainframe-reassignment" src="https://github.com/telkheir/DNS-exercises/assets/145223639/637259a4-19f7-4b68-8884-24cb5c6b5f12">
          <br><br>
          We can observe our device's local DNS cache by typing "ipconfig/displaydns" into the command line. Here we see that 'mainframe' is still associated with the IP address of DC-1.
          <br><br>
          <img width="678" alt="dns-cache-client-1" src="https://github.com/telkheir/DNS-exercises/assets/145223639/a0bdf0d6-353e-4d08-b500-aa341fc69b86">
          <br><br>
          We need to clear the local cache of Client-1 so that it will check the DNS for the updated IP address of 'mainframe'. To do so, type "ipconfig/flushdns" into the command line. You will need to have the command prompt open as an administrator.
          <br><br>
          <img width="675" alt="dns-flush-cache" src="https://github.com/telkheir/DNS-exercises/assets/145223639/5bde58e3-e2dd-4cd9-8dd1-144124fd93f8">
          <br><br>
          Observe the local DNS cache once again by typing "ipconfig/displaydns" into the command line and try to ping "mainframe" once again. The new address (8.8.8.8) should be used this time since there is no longer any record for 'mainframe' in our local cache for our device to default to.
          <br><br>
          <img width="674" alt="dns-flushed-cache-mainframe" src="https://github.com/telkheir/DNS-exercises/assets/145223639/ff1b8828-7052-48e7-9893-6ebd69f3ace9">
      </li>
      <li><h3 id = "step_3">CNAME Record Exercise</h3>
          A CNAME is a record that maps one domain name to another. In this last exercise, we will create a CNAME record that points any device in our domain to Google.com when we try to ping the word 'search'. First, let's test out what happens when we type 'ping search' in the command line of the Client-1 VM.
          <br><br>
          <img width="674" alt="dns-ping-search-fail" src="https://github.com/telkheir/DNS-exercises/assets/145223639/f53a20eb-3e8b-40dd-a720-2d4fd9d3c820">
          <br><br>
          We can see the ping fail because there is no hostname 'search' in our local cache, host file, or DNS. To set up this record, go to the DNS Mangager in DC-1, right click on your domain name in the Forward Lookup Zone folder, and click "New Alias (CNAME)".
          <br><br>
          <img width="647" alt="dns-cname-nav" src="https://github.com/telkheir/DNS-exercises/assets/145223639/d30f65c3-2a53-4694-bfd6-8a6c6e5afc9c">
          <br><br>
          Set the name to 'search' and put Google down as the domain name. Return to the Client-1 device and type "ping search" into the command line again. Our newly made CNAME record should redirect the data packets to Google.com.
          <br><br>
          <img width="676" alt="dns-cname-setup" src="https://github.com/telkheir/DNS-exercises/assets/145223639/227d7993-30b5-488c-9dc0-a1b0b8ab7386">
          <img width="674" alt="dns-ping-search" src="https://github.com/telkheir/DNS-exercises/assets/145223639/8db8d257-77a1-4cc7-a71c-3ed2da4d0b5f">
      </li>
    </ol>
