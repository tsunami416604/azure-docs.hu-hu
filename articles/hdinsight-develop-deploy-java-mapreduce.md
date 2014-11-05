<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight-Hadoop" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for Hadoop in HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="nitinme" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/10/2014" ms.author="nitinme"></tags>

# Develop Java MapReduce programs for Hadoop in HDInsight

This tutorial walks you through an end-to-end scenario for developing a word counting Hadoop MapReduce job in Java using Apache Haven. The tutorial also shows how to test the application on the HDInsight Emulator and then deploy and run it on Azure HDInsight cluster.

**Prerequisites:**

Before you begin this tutorial, you must have completed the following:

-   Install Azure HDInsight Emulator. For instructions, see [Get started using HDInsight Emulator][Get started using HDInsight Emulator].
-   Install Azure PowerShell on the emulator computer. For instructions, see [Install and configure Azure PowerShell][Install and configure Azure PowerShell].
-   Install Java platform JDK 7 or higher on the emulator computer. This is already available on the emulator computer.
-   Install and configure [Apache Maven][Apache Maven].
-   Obtain an Azure subscription. For instructions, see [Purchase Options][Purchase Options], [Member Offers][Member Offers], or [Free Trial][Free Trial].

## In this article

-   [Use Apache Maven to create a word counting MapReduce program in Java][Use Apache Maven to create a word counting MapReduce program in Java]
-   [Test the program on the emulator][Test the program on the emulator]
-   [Upload data files and the application to Azure Blob storage][Upload data files and the application to Azure Blob storage]
-   [Run the MapReduce program on Azure HDInsight][Run the MapReduce program on Azure HDInsight]
-   [Retrieve the MapReduce results][Retrieve the MapReduce results]
-   [Next steps][Next steps]

## <a name="develop"></a>Use Apache Maven to create a MapReduce program in Java

Create a word counting MapReduce application. It is a simple application that counts the occurrences of each word in a given input set. In this section, we will performing the following tasks:

1.  Create a project using Apache Maven
2.  Update the project's object model (POM)
3.  Create the word count MapReduce application
4.  Build and package the application

**To create a project using Maven**

1.  Create a directory \*\*C:\\Tutorials\\WordCountJava\*\*.
2.  From the command-line on your development environment, change directories to the location you created.
3.  Use the **mvn** command, which is installed with Maven, to generate the scaffolding for the project.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    This will create a new directory in the current directory, with the name specified by the **artifactID** parameter (**wordcountjava** in this example.) This directory will contain the following items.

    -   **pom.xml** - the Project Object Model ([POM][POM]) contains information and configuration details used to build the project

    -   **src** - the directory that contains the **main\\java\\org\\apache\\hadoop\\examples** directory, where you will author the application.

4.  Delete the **src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java** file, as it will not be used in this example.

**To update the Project Object Model (POM)**

1.  Edit the **pom.xml** file and add the following inside the `<dependencies>` section.

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
        </dependency>
        <dependency>                                                                                     
          <groupId>org.apache.hadoop</groupId>                                                                                                       
          <artifactId>hadoop-common</artifactId>                                                                                                         
          <version>2.5.1</version>                                                                                            
        </dependency>

    This tells Maven that the project requires the libraries (listed within <artifactid\>) with specific version (listed within <version\>). At compile time, this will be downloaded from the default Maven repository. You can use the [Maven repository search][Maven repository search] to view more.

2.  Add the following to the **pom.xml** file. This must be inside the `<project>...</project>` tags in the file; for example, between `</dependencies>` and `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>       
          </plugins>
        </build>

    This configures the [maven-shade-plugin][maven-shade-plugin], which is used to prevent license duplication in the JAR that is built by Maven. The reason this is used is that the duplicate license files cause an error at run time on the HDInsight cluster. Using maven-shade-plugin with the `ApacheLicenseResourceTransformer` implementation prevents this error.

    The maven-shade-plugin will also produce an uberjar (or fatjar,) that contains all the dependencies required by the application.

3.  Save the **pom.xml** file.

**To create the word count application**

1.  Go to the **wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples** directory and rename the **app.java** file to **WordCount.java**.
2.  Open Notepad.
3.  Copy and paste the following program into notepad.

        package org.apache.hadoop.examples;

        import java.io.IOException;
        import java.util.StringTokenizer;
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.fs.Path;
        import org.apache.hadoop.io.IntWritable;
        import org.apache.hadoop.io.Text;
        import org.apache.hadoop.mapreduce.Job;
        import org.apache.hadoop.mapreduce.Mapper;
        import org.apache.hadoop.mapreduce.Reducer;
        import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
        import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class WordCount {

          public static class TokenizerMapper 
               extends Mapper<Object, Text, Text, IntWritable>{

            private final static IntWritable one = new IntWritable(1);
            private Text word = new Text();

            public void map(Object key, Text value, Context context
                            ) throws IOException, InterruptedException {
              StringTokenizer itr = new StringTokenizer(value.toString());
              while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
              }
            }
          }

          public static class IntSumReducer 
               extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();

            public void reduce(Text key, Iterable<IntWritable> values, 
                               Context context
                               ) throws IOException, InterruptedException {
              int sum = 0;
              for (IntWritable val : values) {
                sum += val.get();
              }
              result.set(sum);
              context.write(key, result);
            }
          }

          public static void main(String[] args) throws Exception {
            Configuration conf = new Configuration();
            String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
            if (otherArgs.length != 2) {
              System.err.println("Usage: wordcount <in> <out>");
              System.exit(2);
            }
            Job job = new Job(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
            FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
            FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
          }
        }

    Notice the package name is **org.apache.hadoop.examples** and the class name is **WordCount**. You will use the names when you submit the MapReduce job.

4.  Save the file.

**To build and package the application**

1.  Open a command prompt and change directories to the **wordcountjava** directory.

2.  Use the following command to build a JAR containing the application.

        mvn clean package

    This will clean any previous build artifacts, download any dependencies that have not already been installed, then build and package the application.

3.  Once the command completes, the **wordcountjava\\target** directory will contain a file named **wordcountjava-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] The **wordcountjava-1.0-SNAPSHOT.jar** file is an uberjar (sometimes called a fatjar,) which contains all the dependencies required to run the application.

## <a name="test"></a>Test the program on emulator

Testing the MapReduce job on the HDInsight Emulator includes the following procedures:

1.  Upload the data files to the HDFS on the emulator
2.  Create a local user group
3.  Run a word counting MapReduce job
4.  Retrieve the job results

By default, HDInsight emulator uses HDFS as the default file system. Optionally, you can configure the HDInsight emulator to use Azure Blob storage. For details, see [Get started with HDInsight Emulator][Get started with HDInsight Emulator].

In this tutorial, you will use the HDFS *copyFromLocal* command to upload the data files to HDFS. The next section shows you how to upload files using Azure PowerShell to Azure Blob storage. For other methods for uploading files to Azure Blob storage, see [Upload data to HDInsight][Upload data to HDInsight].

This tutorial uses the following HDFS folder structure:

|---------------------------|----------------------------------------------------|
| Folder                    | Note                                               |
| /WordCount                | The root folder for the word counting project.     |
| /WordCount/Apps           | The folder for the mapper and reducer executables. |
| /WordCount/Input          | The MapReduce source file folder.                  |
| /WordCount/Output         | The MapReduce output file folder.                  |
| /WordCount/MRStatusOutput | The job output folder.                             |

This tutorial uses the .txt files located in the %hadoop\_home% directory as the data files.

> [WACOM.NOTE] The Hadoop HDFS commands are case sensitive.

**To copy the data files to the emulator HDFS**

1.  Open Hadoop command line from your desktop. Hadoop command line is installed by the emulator installer.
2.  From the Hadoop command line window, run the following command to make a directory for the input files:

        hadoop fs -mkdir /WordCount/Input

    The path used here is the relative path. It is equivalent to the following:

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  Run the following command to copy some text files to the input folder on HDFS:

        hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /WordCount/Input

    The MapReduce job will count the words in these files.

4.  Run the following command to list and verify the uploaded files:

        hadoop fs -ls /WordCount/Input

**To create a local user group**

To successfully run the MapReduce job on the cluster you must create a user group called hdfs. To this group, you must also add a user hadoop and the local user with which you log onto the emulator. Use the following commands from an elevated command prompt:

        # Add a user group called hdfs      
        net localgroup hdfs /add

        # Adds a user called hadoop to the group
        net localgroup hdfs hadoop /add

        # Adds the local user to the group
        net localgroup hdfs <username> /add

**To run the MapReduce job using Hadoop command line**

1.  Open Hadoop command line from your desktop.
2.  Run the following command to delete the /WordCount/Output folder structure from HDFS. /WordCount/Output is the output folder of the word counting MapReduce job. The MapReduce job will fail if the folder already exists. This step is necessary if this is the second time you run the job.

        hadoop fs -rm - r /WordCount/Output

3.  Run the following command:

        hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    If the job completes successfully, you should get an output similar to the following screenshot:

    ![HDI.EMulator.WordCount.Run][HDI.EMulator.WordCount.Run]

    From the screenshot, you can see both map and reduce completed 100%. It also lists the job ID. The same report can be retrieved by opening the **Hadoop MapReduce status** shortcut from your desktop, and looking for the same job ID.

The other option for running a MapReduce job is using Azure PowerShell. For instructions, see [Get started with the HDInsight Emulator][Get started using HDInsight Emulator].

**To display the output from HDFS**

1.  Open Hadoop command line.
2.  Run the following commands to display the output:

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-r-00000

    You can append "|more" at the end of the command to get the page view. Or use the findstr command to find a string pattern:

        hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

Until now, you have developed a word counting MapReduce job, and tested it successfully on the emulator. The next step is to deploy and run it on Azure HDInsight.

## <span id="upload"></span></a>Upload data and application to Azure Blob storage

Azure HDInsight uses Azure Blob storage for data storage. When an HDInsight cluster is provisioned, an Azure Blob storage container is used to store the system files. You can use either this default container or a different container (either on the same Azure storage account or a different storage account located on the same data center as the cluster) for storing the data files.

In this tutorial, you will create a container on a separate storage account for the data files and the MapReduce application. The data files are the text files in the **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** directory on your emulator workstation.

**To create a Blob storage and a container**

1.  Open Azure PowerShell.
2.  Set the variables, and then run the commands:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

    The **$subscripionName** is associated with your Azure subscription. You must name the **$storageAccountName\_Data** and **$containerName\_Data**. For the naming restrictions, see [Naming and Referencing Containers, Blobs, and Metadata][Naming and Referencing Containers, Blobs, and Metadata].

3.  Run the following command to create a storage account and a Blob storage container on the account

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  Run the following commands to verify the storage account and the container:

        Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
        Get-AzureStorageContainer -Context $destContext

**To upload the data files**

1.  Open Azure PowerShell.
2.  Set the first three variables, and then run the commands:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\"
        $destFolder = "WordCount/Input"

    The **$storageAccountName\_Data** and **$containerName\_Data** are the same as you defined in the last procedure.

    Notice the source file folder is **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**, and the destination folder is **WordCount/Input**.

3.  Run the following commands to get a list of the txt files in the source file folder:

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Run the following commands to create a storage context object:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  Run the following commands to copy the files:

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
        }

6.  Run the following command to list the uploaded files:

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    You should see about the uploaded text data files.

**To upload the word counting application**

1.  Open Azure PowerShell.
2.  Set the first three variables, and then run the commands:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
        $blobFolder = "WordCount/jars"

    The **$storageAccountName\_Data** and **$containerName\_Data** are the same as you defined in the last procedure, which means you will upload both the data file and the application to the same container on the same storage account.

    Notice the destination folder is **WordCount/jars**.

3.  Run the following commands to create a storage context object:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  Run the following commands to copy the applications:

        Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  Run the following command to list the uploaded files:

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    You should see the jar file listed there.

## <a name="run"></a>Run the MapReduce job on Azure HDInsight

In this section, you will create a PowerShell script that performs the following tasks:

1.  Provisions an HDInsight cluster

    1.  Create a storage account that will be used as the default HDInsight cluster file system
    2.  Create a Blob storage container
    3.  Create an HDInsight cluster

2.  Submits the MapReduce job

    1.  Create a MapReduce job definition
    2.  Submit a MapReduce job
    3.  Wait for the job to complete
    4.  Display standard error
    5.  Display standard output

3.  Deletes the cluster

    1.  Delete the HDInsight cluster
    2.  Delete the storage account used as the default HDInsight cluster file system

**To run the PowerShell script**

1.  Open Notepad.
2.  Copy and paste the following code:

        # The storage account and the HDInsight cluster variables
        $subscriptionName = "<AzureSubscriptionName>"
        $stringPrefix = "<StringForPrefix>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = <NumberOFNodesInTheCluster>

        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"

        $clusterName = $stringPrefix + "hdicluster"

        $storageAccountName_Default = $stringPrefix + "hdistore"
        $containerName_Default =  $stringPrefix + "hdicluster"

        # The MapReduce job variables
        $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
        $className = "org.apache.hadoop.examples.WordCount"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        # Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
        $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account used as the default file system
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container used as teh default file system
        Write-Host "Create a Blob storage container" -ForegroundColor Green
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

        New-AzureStorageContainer -Name $containerName_Default -Context $destContext

        #=============================
        # Create an HDInsight cluster
        Write-Host "Create an HDInsight cluster" -ForegroundColor Green
        $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

        New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config

        #=============================
        # Create a MapReduce job definition
        Write-Host "Create a MapReduce job definition" -ForegroundColor Green
        $mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus

        #=============================
        # Run the MapReduce job
        Write-Host "Run the MapReduce job" -ForegroundColor Green
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

        #=============================
        # Delete the HDInsight cluster
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Remove-AzureHDInsightCluster -Name $clusterName  

        # Delete the default file system storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Set the first six variables in the script. **$stringPrefix** is used to prefix the specified string to the HDInsight cluster name, the storage account name, and the Blob storage container name. Because the names for these must be between 3 and 24 characters, make sure the string you specify and the names this script uses, together does not exceed the character limit for the name. You must use all lower case for the **$stringPrefix**.

    **$storageAccountName\_Data** and **$containerName\_Data** are the storage account and container that are used for storing the data files and the application. **$location** must match the data storage account location.

4.  Review the rest of the variables.
5.  Save the script file.
6.  Open Azure PowerShell.
7.  Run the following command to set the execution policy to remotesigned:

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  When prompted, enter username and password for the HDInsight cluster. Because you will delete the cluster at the end of the script and you will not need the username and password anymore, the username and password can be any strings. If you don't want to get prompted for the credentials, see [Working with Passwords, Secure Strings and Credentials in Windows PowerShell][Working with Passwords, Secure Strings and Credentials in Windows PowerShell]

## <a name="retrieve"></a>Retrieve the MapReduce job output

This section shows you how to download and display the output. For the information on displaying the results on Excel, see [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][Connect Excel to HDInsight with the Microsoft Hive ODBC Driver], and [Connect Excel to HDInsight with Power Query][Connect Excel to HDInsight with Power Query].

**To retrieve the output**

1.  Open Azure PowerShell window.
2.  Change directory to **C:\\Tutorials\\WordCountJava**. The default Azure PowerShell folder is **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. The cmdlets you will run will download the output file to the current folder. You don't have permissions to download the files to the system folders.
3.  Run the following commands to set the values:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-r-00000"

4.  Run the following commands to create an Azure storage contect object:

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  Run the following commands to download and display the output:

        Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

After the job is completed, you have the options to export the data to SQL Server or Azure SQL database using [Sqoop][Sqoop], or to export the data to Excel.

## <span id="nextsteps"></span></a>Next steps

In this tutorial, you have learned how to develop a Java MapReduce job, how to test the application on HDInsight emulator, and how to write a PowerShell script to provision an HDInsight cluster and run a MapReduce on the cluster. To learn more, see the following articles:

-   [Develop C# Hadoop streaming MapReduce programs for HDInsight][Develop C# Hadoop streaming MapReduce programs for HDInsight]
-   [Get started with Azure HDInsight][Get started with Azure HDInsight]
-   [Get started with the HDInsight Emulator][Get started using HDInsight Emulator]
-   [Use Azure Blob storage with HDInsight][Use Azure Blob storage with HDInsight]
-   [Administer HDInsight using PowerShell][Administer HDInsight using PowerShell]
-   [Upload data to HDInsight][Upload data to HDInsight]
-   [Use Hive with HDInsight][Use Hive with HDInsight]
-   [Use Pig with HDInsight][Use Pig with HDInsight]
-   [Connect Excel to HDInsight with Power Query][Connect Excel to HDInsight with Power Query]
-   [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver][Connect Excel to HDInsight with the Microsoft Hive ODBC Driver]

  [Get started using HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Install and configure Azure PowerShell]: ../install-configure-powershell/
  [Apache Maven]: http://maven.apache.org/
  [Purchase Options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Member Offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Free Trial]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Use Apache Maven to create a word counting MapReduce program in Java]: #develop
  [Test the program on the emulator]: #test
  [Upload data files and the application to Azure Blob storage]: #upload
  [Run the MapReduce program on Azure HDInsight]: #run
  [Retrieve the MapReduce results]: #retrieve
  [Next steps]: #nextsteps
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [Maven repository search]: http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [Get started with HDInsight Emulator]: ../hdinsight-get-started-emulator/#blobstorage
  [Upload data to HDInsight]: ../hdinsight-upload-data/
  [HDI.EMulator.WordCount.Run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png
  [Naming and Referencing Containers, Blobs, and Metadata]: http://msdn.microsoft.com/en-us/library/windowsazure/dd135715.aspx
  [Working with Passwords, Secure Strings and Credentials in Windows PowerShell]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Connect Excel to HDInsight with the Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Connect Excel to HDInsight with Power Query]: ../hdinsight-connect-excel-power-query/
  [Sqoop]: ../hdinsight-use-sqoop/
  [Develop C# Hadoop streaming MapReduce programs for HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Get started with Azure HDInsight]: ../hdinsight-get-started/
  [Use Azure Blob storage with HDInsight]: ../hdinsight-use-blob-storage/
  [Administer HDInsight using PowerShell]: ../hdinsight-administer-use-powershell/
  [Use Hive with HDInsight]: ../hdinsight-use-hive/
  [Use Pig with HDInsight]: ../hdinsight-use-pig/
