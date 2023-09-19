<h1>DNS Exercises: Local Cache, A-Records, and CNAMES</h1>

The purpose of this tutorial is to gain an understanding of some of the workings of DNS through concepts like A-records and CNAME records. We will also explore our local DNS cache and roothairs. This project was a follow up to the <a href = "https://github.com/telkheir/implementing-active-directory">implementing Active Directory in Azure</a> tutorial.


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
          First we're going to use Remote Desktop to access both DC-1 and Client-1 as an admin. In my example, I logged in as tekdomain.com\tek_admin. We're going to try to ping 'mainframe' from the Client-1 VM. The Client-1 VM will first search the local cache and when it cannot find the domain name there, it will then search the host file, where it again should fail to find the domain name, before it checks the DNS. This ping should fail because 'mainframe' does not exist in our domain's records yet.
          <br><br>
          [image - cmd line fail]
          <br><br>
          In the command line type "nslookup mainframe". This should also fail because there is no DNS record.
          <br><br>
          [image - cmd line nslookup fail]
          <br><br>
          Now we are going to switch to the DC-1 VM to register 'mainframe' as a domain name in our device and any other that may log into our domain. To do so, go to the Server Manager on DC-1 and in the top right corner, click on Tools to see a drop-down menu and select DNS. Double click DC-1 and enter your domain name to view a list of all of the device's A-records.
      <br><br>
      [image - a-records in server manager]
      <blockquote>
           An A-record is a DNS record used to map a hostname to its respective IPv4 address.
          Forward Lookup Zones refer to hostname to IPv4 mapping
          Reverse Lookup Zones refer to IPv4 to hostname mapping
      </blockquote>
      <br>
      Right click on your domain name and select New Host (A or AAAA). Name it 'Mainframe' and set the IP address to that of the DC-1 VM. 'Mainframe' should now be added to our domain's list of A-records. If we try to ping 'mainframe' from Client-1 once again, it will send data packets to the DC-1 VM.
          <br><br>
          [image - adding new arecord and/or pinging mainframe successfully]
          <br><br>
          Typing 'ipconfig / displaydns' into the command line will also give you a list of known hostnames and IPv4 addresses stroed on that device's local cache.
      </li>
      <li><h3 id = "step_2">Lcal DNS Cache Exercise</h3></li>
      <li><h3 id = "step_3">CNAME Record Exercise</h3></li>
    </ol>
