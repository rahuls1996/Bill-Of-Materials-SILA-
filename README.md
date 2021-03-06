# Bill-Of-Materials-SILA-
Bill of Materials Database challenge for SILA + Bonus solution. 
Challenge - Modelling, Manipulating and Querying a Bill of Materials for a pen. The pen has can be made of plastic or metal, and can come in blue or red.  

RDBMS used - MYSQL

Initial Assumptions - Given the challenge, taking the following assumptions into consideration will help give more context to my data model - 
While populating the initial data into the tables, I assumed the presence of the following top level assemblies  -  1. Top Barrel Assembly  2. Rest of Pen Assembly 3. Box Assembly. 
Within the Rest of Pen Assembly, we have the Cartridge Assembly and the Bottom Barrel Assembly. 
If a part is removed from it’s assembly, it’s new “parent” assembly becomes it’s previous parent’s parent. For example Cartridge Body is a child of Cartridge Assembled. Furthermore, Cartridge Assembled is a child of Rest of Pen Assembled. If we remove Cartridge Body from it’s assembly i.e. Cartridge Assembled, Cartridge Body’s new parent becomes Rest of Pen Assembled. 
If a part is which is also an assembly, like Cartridge Assembled, is deleted, the constituent parts of this assembly get assigned to Cartridge Assembled’s parent. This is going by the problem statement, which states that Assembled components are tracked as parts (and therefore I assumed “deletion” of an assembled part as the deletion of a dedicated assembly for its constituent parts).
A pen can come in the following varieties - Metal Blue, Metal Red, Plastic Blue, Plastic Red. 
The Rubber Grip, Clip, and Ink signify the pens colour.
The material of the Top and Bottom Barrels and the Clip signify the material of the pen i.e. Plastic or Metal. 
If a new part is inserted, it is simply inserted into the top level of the hierarchy, i.e. the parent is NULL.
If a new parent - child relationship is created, the following are taken into consideration -
If the parent - child relationship already exists, nothing is done. 
If the child exists in the table and the new parent (NP) does not, the child’s original parent (OP) is made NP’s parent, and the child’s parent field is updated with NP.
If both parent and child exist somewhere in the table, the child’s parent is simply updated. 
If the parent exists and child does not, values are simply inserted. 
If none of these are true, the parent-child relationship is simply inserted, and the parent is considered a top level parent. 
When listing all parts, assembled parts are listed too, as we are tracking them as parts.



SETUP:
First, load the instantiation.sql file in the chosen MYSQL IDE. In my case, it was RazorSQL. Once loaded, we need to execute all the different queries in this file. I have used delimiters to separate different queries, so this can be done by the click of one button in the IDE. For example, in RazorSQL, this is an execute all button in the shape of a downward green arrow. Execution of this file will create and populate our different tables. 
In a separate window load and execute, in the same way as above, the file titled Combined.sql. This will create and load all of the stored procedures created for this assigned task. 
Now, Load the Callers.sql file. This contains 13 procedure calls, one for each of the assigned tasks. Parameters are pre-loaded, and be sure to specify the type of pen (Metal Red, Plastic Red, Metal Blue, Plastic Blue) that you would like to query. 

Data Model Explained: I looked at the Bill of Materials of a pen as a hierarchical model with a tree structure. Top level assemblies form the top of the tree, with their subsequent sub assemblies beneath them. Component parts form the ‘leaf’ nodes. I represented the following information in a single table using an Adjacency list format. This means that my table had three columns - Part ID, part_name and it’s corresponding parent.


With parts having ‘NULL’ as their parent being nodes at the top level. Now, given that we have four varieties of pens, there are four tables. Depending on the variety selected, the appropriate table is queried.


Bonus Exercise. 

As it can be seen from the source code, insertion into an Adjacency List hierarchy is not very costly. However, traversing up and down the tree structure is somewhat complicated. Therefore, as a bonus, I suggest the following change - Use an Adjacency List structure for insertions / deletions / updating, and for traversing up/down we could use a different model - a Nested Set. I have represented the following using a left value and a right value for each part, with component parts having a difference of 1 between left and right. The parent assembly has a left and right value that envolopes it's children. For example, if top barrel and clip are children of top barrel assembled, top barrel will have left value = 2, right = 3, Clip will have left = 4, right = 5, and the top barrel assembled will have a left value of 1 and a right value of 6.

Basically, we are viewing the entire Pen assembly as a Set, with its constituent top level assemblies as ‘Nested’ sets to it. Furthermore, sub assemblies to the top level assemblies are considered as Nested sets to them. A table of this format looks like the following - 
Therefore, as a conclusion - The Adjacency list approach is robust, but it does not hold well for traversing up and down the tree. Here the nested set approach comes in handy, where due to the arrangement of the table traversing up and down is super easy as shown by the stored procedures I have written for this purpose. 

First, run nested.sql. Followed by this, open the "BonusCallers" file and query the given functions!

Thank you! 
