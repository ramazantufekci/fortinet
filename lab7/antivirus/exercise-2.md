Exercise 2: Using Antivirus Scanning in Proxy-Based Inspection Mode
In this exercise, you will examine how to use antivirus in proxy-based inspection mode to understand how FortiGate performs antivirus scanning. You will observe the behavior of antivirus scanning, with and without deep inspection, to understand the importance of performing full-content inspection.

Change the Inspection Mode in an Antivirus Profile
You will change the inspection mode in the default antivirus profile, which is applied to the firewall policy, to inspect traffic.

To change the inspection mode in an antivirus profile
Connect to the Local-FortiGate GUI, and then log in with the username admin and password password.
Click Security Profiles > AntiVirus.
Right-click the default antivirus profile, and then click Edit.
In the Feature set field, select Proxy-based.


Click OK.
Change the Inspection Mode in a Firewall Policy
Inspection mode is configured on a per-policy basis on FortiGate. You will change the inspection mode from flow-based to proxy-based.

Take the Expert Challenge!
On the Local-FortiGate GUI, complete the following:

Edit the Full_Access firewall policy, and change the Inspection Mode to Proxy-based.
Enable the default antivirus profile.
Use the certificate-inspection profile for SSL inspection.
If you require assistance, or to verify your work, use the step-by-step instructions that follow.

After you complete the challenge, see Test the Antivirus Configuration on page 1.

To change the inspection mode in a firewall policy
Continuing on the Local-FortiGate GUI, click Policy & Objects > Firewall Policy.
Double-click the Full_Access policy to edit it.
In the Inspection Mode field, select Proxy-based.


In the Protocol Options field, verify that the default profile is selected.
In the Security Profiles section, in the AntiVirus field, verify that the default profile is selected.
In the SSL Inspection field, keep the default certificate-inspection profile.
	
The Protocol Options profile provides the required settings to hold traffic in proxy while the inspection process is carried out. The default profile is preconfigured to follow the standardized parameters for the common protocols used in networking.

SSL Inspection selects the certificate-inspection profile by default. You can select any preconfigured SSL inspection profile in the associated field.

Keep the default values for the remaining settings, and then click OK to save the changes.
Test the Antivirus Configuration
You will download the EICAR test file to your Local-Client VM. The EICAR test file is an industry-standard virus used to test antivirus detection without causing damage. The file contains the following characters:

X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*

To test the antivirus configuration
On the Local-Client VM, open a browser, and then access the following website:
http://10.200.1.254/test_av.html

In the Download area section, download any EICAR sample file.


FortiGate should block the download attempt, and insert a replacement message similar to the following example:



Stop and think!

Why can FortiGate display a replacement message?

In proxy-based inspection mode, the file is buffered. If a virus is detected, FortiGate can then replace the file by a message, which provides security information.

Test an Alternate Download Method
You will test the proxy-based antivirus configuration using the Save Link As method to download the EICAR text file.

To test the antivirus configuration
On the Local-Client VM, open a new browser tab, and then go to the following website:
http://10.200.1.254/test_av.html

In the Download area section, right-click eicar.com.txt, and then select Save Link As.


Change the download location to Desktop, and then click Save.
You should see the file you downloaded on the desktop. Why was the download allowed?

On your desktop, right-click the eicar.com.txt downloaded file, click Open With Other Application, click Notepad++, click Select to open the file you downloaded, and then scroll to read the bottom of the text file.
Is the content of the file what it is supposed to be?

Stop and think!

Remember, you are using proxy-based inspection mode. When a firewall policy inspection mode is set to proxy, traffic flowing through the policy is buffered by FortiGate for inspection. This means that FortiGate holds the packets for a file, email, or web page until the entire payload is inspected for violations (virus, spam, or malicious web links). After FortiOS has finished the inspection, FortiGate either releases the payload to the destination (if traffic is clean) or drops and replaces it with a message (if the traffic contains violations). FortiGate injects the block message into the partially downloaded file. The client can use Notepad to open and view the file.

Close Notepad++.
Delete the downloaded eicar.com.txt file from the desktop.
View the Antivirus Logs
You will check and confirm the logs for the tests you just performed.

To view the antivirus logs
Continuing on the Local-FortiGate GUI, click Log & Report > Forward Traffic.
You may need to remove any log filter in the search bar and increase the time frame.

Locate the antivirus log message, and then double-click it.
The Details tab shows forward traffic log information, along with the action taken.



Select the Security tab to view security logs.
Security logs provide information that is more specific to security events, such as filename, virus or botnet, and reference.



Click Log & Report > Security Events > AntiVirus to view antivirus security logs.
The logs should be similar to the following example:



	You can click Details to view more virus information related to an antivirus log entry.
Enable SSL Inspection in a Firewall Policy
So far, you have tested unencrypted traffic for antivirus scanning. In order for FortiGate to inspect the encrypted traffic, you must enable deep inspection in the firewall policy. After you enable this feature, FortiGate can inspect SSL traffic using a technique similar to a man-in-the-middle (MITM) attack.

Take the Expert Challenge!
On Local-Client, test the configuration by downloading the eicar.com file using HTTPS, without enabling the deep-inspection profile in the Full Access firewall policy.
Configure Local-FortiGate to scan secure protocols by enabling SSL Inspection, using the deep-inspection profile in the Full Access firewall policy.
Test the configuration by downloading the eicar.com file using HTTPS.
If you require assistance, or to verify your work, use the step-by-step instructions that follow.

After you complete the challenge, seeReview the Antivirus History on page 1.

To test antivirus scanning without SSL inspection enabled in the firewall policy
On the Local-Client VM, open a browser, and then go to the following website:
https://10.200.1.254/test_av.html

Click Advanced.
Click Accept the Risk and Continue.
In the Download area section, download the eicar.com sample file.


FortiGate should not block the file, because you did not enable full SSL inspection.

On the Local-Client VM, close the browser.
To enable and test the SSL inspection profile in a firewall policy
Return to the Local-FortiGate GUI, and then click Policy & Objects > Firewall Policy.
Double-click the Full Access firewall policy to edit it.
In the Security Profiles section, in the SSL Inspection field, select deep-inspection.
Keep the remaining default settings, and then click OK to save the changes.
Click OK to confirm.
On the Local-Client VM, open a browser, and then go to the same website:
https://10.200.1.254/test_av.html

In the Download area section, try to download the same eicar.com file again.
	
If the FortiGate self-signed full-inspection certificate is not installed in the browser, end users will see a certificate warning message. In this environment, the FortiGate self-signed SSL inspection certificate is installed in the browser. If the block page does not appear after 2 minutes, close all browser tabs, and then restart the browser.

You may also need to clear your cache. In Firefox, click Settings > Privacy & Security. Scroll to History, click Clear History, and then ensure the time range to clear is set to Everything. Click OK.

FortiGate should block the download and replace it with a message.

Review the Antivirus History
You will check the security history and the virus definition status.

To view the security history
Continuing on the Local-FortiGate GUI, click Dashboard > Security > Top Threats by Threat Level.
The graph should be similar to the following example:



	You can click Drill down to view more details about a specific threat.
To verify the antivirus definitions status
Connect over SSH to Local-FortiGate.
Log in with the username admin and password password.
Enter the following commands:
diagnose debug application update -1

diagnose debug enable

execute update-av

After a few seconds, the output should include information similar to the following example:



	The output confirms that the device has the latest antivirus packages for correct protection.
Enter the following commands:
diagnose debug disable

diagnose debug application update 0

Log out of the SSH session and Local-FortiGate GUI.
LAB-7 > Using Antivirus Scanning in Proxy-Based Inspection Mode
