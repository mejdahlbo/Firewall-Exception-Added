# Splunk - Monitoring Firewall Exception Changes

## Objective
We have been set the task by our SOC manager to add a new panel onto our existing dashboard **Windows Activity** We want to look for any changes made to the **Windows Firewall Exception List**, specifically **rules that were added.**

The panel should include the **time, Computer, Profile Changed, Rule Name and Rule ID** and the time should be sorted starting from **earliest to latest** . The new panel should be named **Firewall Exception Added.**

### Skills Learned

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and add new panel to existing dashboard.

### Prerequisites 

-   VM - _(WMware Workstation)_
-   Ubuntu Server - _(24.04.1 LTS)_
-   Splunk Enterprise _(9.3.1)_
-   Index file to use in Splunk _(5931 Events)_
-   Internet Browser _(Vivaldi 6.9 (3447.51))_

## Thought process and Steps
**Making sure we are using the correct index file for the lab**

_We are using the command ”**index="mydfir-lab1**” to check this and confirming that_ events read **5931**, next we are ensuring that the time is correctly set which is necessary for this file → _**07-01 to 08-01-2024**_.
<br>
![Ref 1: Checking Index File](https://i.imgur.com/zy2lUPP.jpeg)
<br>
*Ref 1: Checking for the Index File*

### Building our Search

We are looking for changes in the Windows Firewall Exception List. We can then specify that we only want to search Windows machines, in the search we add **winvm,** _command “**index="mydfir-lab1" host=winvm**”._ A new search gets us **993** event.
![Ref 2: Test Narrowing Down the Search to Windows Machines](https://i.imgur.com/rfX2a4w.jpeg)
<br>
*Ref 2: Test Narrowing Down the Search to Windows Machines*

Next, as we are looking for Windows Firewall Exception changes, we need to know the event ID for when rules are added. We look the event ID (Event Code) for Windows Firewall when rules are added by using this [site](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/).
<br><br>
![Ref 3: Looking up event ID for Windows Firewall Changes](https://i.imgur.com/SAK7S86.jpeg)
<br>
*Ref 3: Looking up event ID for Windows Firewall Changes*
<br><br>
![Ref 4: Looking up info on event ID 4946](https://i.imgur.com/tfKCV8U.jpeg)
<br>
*Ref 4: Looking up info on event ID 4946*

We see the code is **4946** and add it to our search query, command “**index="mydfir-lab1" host="winvm" EventCode=4946**” which gives us **53** events.
<br>
![Using event ID in our Search](https://i.imgur.com/gSReHXS.jpeg)
<br>
*Ref 5: Using event ID in our Search*

Lastly we want to extract the following data about each event time, **ComputerName, Profile_Changed, Rule_Name, Rule_ID**.
![Ref 6: ComputerName Field](https://i.imgur.com/T3TzaUE.jpeg)
![Ref 7: Profile Changed Field](https://i.imgur.com/CsMO8Wx.jpeg)
![Ref 8: Rule Name Field](https://i.imgur.com/14kMpdJ.jpeg)
![Ref 9: Rule ID Field](https://i.imgur.com/5pV7T2e.jpeg)
<br>

*Ref 6: ComputerName Field <br>
Ref 7: Profile Changed Field <br>
Ref 8: Rule Name Field <br>
Ref 9: Rule ID Field* <br>

We add this to our existing search by using the pipe character command **|stats count by _time, ComputerName, Profile_Changed, Rule_Name, Rule_ID**
<br>
![Ref 10: Search criteria Added](https://i.imgur.com/TF2Ol2k.jpeg)
<br>
*Ref 10: Search criteria Added*
<br>
![Ref 11: Search Result](https://i.imgur.com/KtGfItC.jpeg)
*Ref 11: Search Result*

### Sorting our Data

We have got our data and now we wish to sort it from earliest to latest. We add the pipe character to add the _command_ to our search query **| sort +_time**

Our final search _command_ query looks like this

**index="mydfir-lab1" host="winvm" EventCode=4946 |stats count by _time, ComputerName, Profile_Changed, Rule_Name, Rule_DI | sort +_time**
<br>
![Ref 12: Sorting Search Result](https://i.imgur.com/VL530Ko.jpeg)
<br>
*Ref 12: Sorting Search Result*

### Creating our Panel

The panel we create through the **Save As** option and add it to our existing dashboard called **Windows Activity** and name it **Firewall Exception Added**
<br>
![Ref 13: Adding Panel](https://i.imgur.com/4c5kJSO.jpeg)
<br>
*Ref 13: Adding Panel*
<br>
![Ref 14: Naming New Panel](https://i.imgur.com/0oUiiIt.jpeg)
<br>
*Ref 14: Naming New Panel*

After adding the panel we can check our updated dashboard.
![Ref 15: Panel Added](https://i.imgur.com/zyQ3sCc.jpeg)
*Ref 15: Panel Added*

### Final Thoughts

Through the steps outlined, we successfully created a new panel in the dashboard named **Firewall Exception Added**, which displays the added firewall rules along with the relevant details such as **time, ComputerName, Profile Changed, Rule Name,** and **Rule ID**. By applying filters such as **EventCode=4946** and sorting the events from **earliest to latest**, we were able to focus on specific changes to the **Windows Firewall Exception List**. The data is now presented in a clear and organized manner, aiding in efficient monitoring of firewall rule changes. This panel will assist the SOC team in quickly identifying any new firewall exceptions that could indicate potential security risks.

### Changelog
