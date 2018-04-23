# Index mysql data to Solr
 1. First of all, copy the appropriate libraries that are required to use Data Import Handler. So, let's create the dih folder anywhere on your system (I created it in the Solr home directory, so the path for the jar is relatively set.), and place apache-solr-dataimporthandler-4.x.jar and apache solr-dataimporthandler-extras-4.x.jar from the Solr distribution dist directory in the folder. In addition to that, we need the following entry to be added to the solrconfig.xml file:

<lib dir="../../../dih" regex=".*\.jar" />

2. Next we need to modify the solrconfig.xml file. You should add an entry like the following code:

 <requestHandler name="/dataimport" class="org.apache.solr.handler.dataimport.DataImportHandler">
    <lst name="defaults">
        <str name="config">db-data-config.xml</str>
    </lst>
</requestHandler>

3. Now we will create the db-data-config.xml file that is responsible for the Data Import Handler configuration. It should have contents like the following example:
+sdf
<dataConfig>
    <dataSource driver="com.mysql.jdbc.Driver" url="jdbc:mysql://localhost:3306/test" user="root" password="root" />
    <document>
        <entity name="Employee" query="SELECT EmpID, LastName, FirstName, City from Employee">
            <field column="EmpID" name="id" />
            <field column="LastName" name="LastName" />
            <field column="FirstName" name="FirstName" />
            <field column="City" name="City" />
        </entity>
    </document>
</dataConfig>
+
If you want to use other database engines, please change the driver, url, and user and password attributes.

4. Now, let's create a sample index structure. To do that we need to modify the fields section of the schema.xml file to something like the following snippet:

<field name="id" type="int" indexed="true" stored="true" required="true"/>
<field name="LastName" type="text_general" indexed="true" stored="true" />
<field name="FirstName" type="text_general" indexed="true" stored="true"/>
<field name="City" type="text_general" indexed="true" stored="true"/>

5. One more thing before the indexing – you should copy an appropriate JDBC driver to the lib directory of your Solr installation or the dih directory we created before.

6. Now we can start indexing. Run the following query to Solr:

http://localhost:8983/solr/dataimport?command=full-import

7. Open Solr admin, and check whether the data is indexed.
