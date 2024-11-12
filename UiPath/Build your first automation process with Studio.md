* [index](index.md)

# 「Build your first automation process with Studio」

## 目標
 Build a new automation process that consolidates data from **multiple Excel reports** into one and creates a **pivot table and chart**.

---

## Business requirements:

•
**Streamline** the process by integrating data from multiple reports and creating a single dashboard view.

•
Timely reporting allowing the Sales team to take decisions based on **real time** data.

•
Improved data quality (automation will allow **validation** of the data from the coffee shops before it's merged and processed).

•
If the number of shops increases, automation can be **scalable** to handle more data.

•
Reduced manual effort.

•
Risk mitigation (automating the process will reduce errors that could happen when copying and pasting the data manually).


## WORK FLOW

~~~
BA分析，做文件(PDD)，PJ签字，设计(SDD)，制作
（没这么多人就全自己来）
~~~
1. ### Capturing the 'as-is' and 'to-be' process steps
    Capturing the **'as-is'** and **'to-be'** process steps
Usually, in an automation project implementation, the **Business Analyst** is the one who captures all the 'as-is' and 'to-be' process steps and any other documented details from the **subject matter experts (SME)** and the business team.

2. ### Documenting the process steps
   Afterwards, the process steps are thoroughly documented within the **Process Definition Document (PDD)** and validated with the Solution Architect. 

3. ### Signing-off the PDD
    It is the **Project Manager**'s responsibility to obtain **sign-off** on the documented PDD from the business team.

4. ### Designing the solution

   Once the business requirements of a process are finalized, the Solution Design stage of the implementation process begins. During this stage, the **Solution Architect** designs **a future state flow** and maps out the various modules to be developed to complete the automation.

5. ### Developing the solution

   Next, the **Automation Developers** `create the modules` outlined in the design whiteboard using the **PDD** and **Solution Design Document (SDD)** as references.



## There are although, some **important aspects** we should consider before diving in.

Here are some examples:
1. Deciding between **attended or unattended** automation is the first important decision that impacts `how we build the code`.
Making a change to the robot type later in the phase may prove to be quite complicated.

2. Choosing the correct type of **layout** for the project in Studio is also essential. We can choose from **sequences, flowcharts, and state machines**.

3.  We must also ensure that we have installed the required **dependencies** to use the activities needed for the automation. 

4.   When developing workflows, we should also consider automation **best** practices, such as using a **centralized knowledge repository, source control, Workflow Analyzer**, etc.