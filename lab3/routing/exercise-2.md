Exercise 2: Configuring Equal-Cost Multi-Path Routing
In this exercise, you will configure equal-cost multi-path (ECMP) routing on Local-FortiGate to load balance the internet traffic between port1 and port2.

Configure Administrative Distance
To establish ECMP, first, you will configure multiple static routes with the same administrative distance.

Take the Expert Challenge!
On the Local-FortiGate GUI (admin/password), complete the following:

Change the port2 static route Administrative Distance to 10.
Verify that both port1 and port2 default routes are present in the routing table.
If you require assistance, or to verify your work, use the step-by-step instructions that follow.

After you complete the challenge, see Change the ECMP Load Balancing Algorithm on page 1.

 

To configure administrative distance
Connect to the Local-FortiGate GUI, and then log in with the username admin and password password.
Click Network > Static Routes.
Double-click the port2 static route to edit it.
In the Administrative Distance field, change the value to 10.


Click OK.
To verify the routing table
Continuing on the Local-FortiGate GUI, click Dashboard > Network, and then click Static & Dynamic Routing to expand it to full screen.
Verify that both default routes are installed in the routing table.


Change the ECMP Load Balancing Algorithm
By default, the ECMP load balancing algorithm is based on the source IP address. This works well when there are multiple clients generating traffic. In the lab network, because you have only one client (the Local-Client VM), the source IP address method does not balance any traffic to the second route. FortiGate always uses only one route. For this reason, you will change the load balancing method to use both source and destination IP addresses. Using this method, as long as the traffic goes to multiple destination IP addresses, FortiGate load balances the traffic across both routes.

To modify the ECMP load balancing method
On the Local-FortiGate CLI, log in with the username admin and password password.
Enter the following commands to change the ECMP load balancing method:
config system settings

set v4-ecmp-mode source-dest-ip-based

end

 

Leave the Local-FortiGate CLI session open.
Verify Traffic Routing
You will generate some HTTP traffic and verify traffic routing using the Forward Traffic logs.

Take the Expert Challenge!
On the Local-Client VM, open a few new browser tabs, and then generate some HTTP traffic.
Verify the traffic routing on Local-FortiGate, using the Forward Traffic logs.
Identify why all the outgoing packets are still being routed through port1.
If you require assistance, or to verify your work, use the step-by-step instructions that follow.

After you complete the challenge, see Configure Priority on page 1.

 

To verify traffic routing
On the Local-Client VM, in the browser, open a few new tabs, and then visit a few websites, such as:
http://neverssl.com
http://example.com
http://eu.httpbin.org
On the Local-FortiGate GUI, click Log & Report > Forward Traffic.
In the relevant log entries for the websites you accessed, identify the Destination Interface.


Why are all the outgoing packets still being routed through port1?

Stop and think!

The port2 route is not being used to route internet traffic. Why?

At the beginning of this exercise, you set a distance of 10 on the port2 route but you didn't change its priority. The port2 route priority is still 5, as you configured it in the previous exercise (see Configure a Second Default Route on page 1). In addition, the port1 route has distance and priority values of 10 and 1, respectively.

When two routes to the same destination have the same distance, both remain in the routing table. However, if the priorities are different, FortiGate uses the route with the lowest priority value—port1 in this case. To achieve ECMP with static routes, the distance and priority values must be the same for all routes.

Configure Priority
You will change the priority value for the port2 route to match the port1 route.

Take the Expert Challenge!
On the Local-FortiGate GUI, modify the static routing configuration so both default routes are eligible for ECMP.

If you require assistance, or to verify your work, use the step-by-step instructions that follow.

After you complete the challenge, see Verify ECMP on page 1

 

To configure priority
Continuing on the Local-FortiGate GUI, click Network > Static Routes.
Double-click the port2 default route to edit it.
Click + to expand the Advanced Options section.
Change the Priority value to 1.
Click OK.
Verify ECMP
Now that both port1 and port2 routes share the same distance and priority values, they are eligible for ECMP. First, you will verify the routing table, and then you will verify traffic routing using the Forward Traffic logs.

To verify the routing table
Return to the Local-FortiGate CLI session, and then enter the following command on Local-FortiGate:
get router info routing-table all

 

Verify that both default routes are currently active.


To configure the CLI sniffer
Continuing on the Local-FortiGate CLI session, enter the following command:
diagnose sniffer packet any 'not host 172.16.100.1 and not host 172.16.100.3 and tcp[13]&2==2 and port 80' 4

	The filter 'tcp[13]&2==2' matches packets with the SYN flag on, so the output will show all SYN packets for port 80 (HTTP).
Leave the Local-FortiGate CLI window open in the background.
To verify ECMP routing
On the Local-Client VM, in the browser, open a few new tabs, and then visit a few websites, such as:
http://neverssl.com
http://example.com
http://eu.httpbin.org
Return to the Local-FortiGate CLI session, and then press Ctrl+C to stop the sniffer.
Analyze the sniffer output.


The SYN packets are egressing both port1 and port2. This verifies that Local-FortiGate is now load balancing all internet traffic across both routes.

On the Local-FortiGate GUI, click Log & Report > Forward Traffic.
Identify the Destination Interface in the relevant log entries for the websites you accessed.


LAB-3 > Configuring Equal Cost Multi-Path Routing
