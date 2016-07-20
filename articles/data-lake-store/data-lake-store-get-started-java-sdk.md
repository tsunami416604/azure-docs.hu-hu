<properties
   pageTitle="A Data Lake Store Java SDK használata alkalmazások fejlesztéséhez | Azure"
   description="Az Azure Data Lake Store Java SDK használata alkalmazások fejlesztéséhez"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Az Azure Data Lake Store használatának első lépései a Java használatával

> [AZURE.SELECTOR]
- [Portál](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Ismerje meg, hogyan hozhat létre Azure Data Lake-fiókot az Azure Data Lake Store Java SDK használatával, illetve hogyan végezhet el olyan alapvető műveleteket, mint például a mappák létrehozása, adatfájlok le- és feltöltése, a fiók törlése stb. További információk a Data Lake-ről: [Azure Data Lake Store](data-lake-store-overview.md).

## Azure Data Lake Store Java SDK

Az alábbi hivatkozások megadják a Data Lake Store-hoz készült Java SDK letöltési helyét és a Java SDK referenciáit. A jelen oktatóanyaghoz nem szükséges az SDK letöltése és a referenciadokumentum követése. A hivatkozások csak tájékoztató céllal szerepelnek itt.

* A Data Lake Store-hoz készült Java SDK forráskódja elérhető a [GitHubon](https://github.com/Azure/azure-sdk-for-java).
* A Data Lake Store-hoz készült Java SDK dokumentációja a következő címen érhető el: [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Előfeltételek

* Java fejlesztői készlet (JDK) 8 (a Java 1.8-as verzióját használja).
* IntelliJ vagy egyéb megfelelő Java fejlesztőkörnyezet. Nem kötelező, de ajánlott. Az alábbi utasítások az IntelliJ-t használják.
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure-előfizetés engedélyezése** a Data Lake Store nyilvános előzetes verziójához. Lásd az [utasításokat](data-lake-store-get-started-portal.md#signup).
* **Egy Azure Active Directory-alkalmazás létrehozása**. Az Azure Active Directory használatával történő hitelesítésnek két módja van – **interaktív** és **nem interaktív**. A hitelesítés kívánt módjának megfelelően eltérő előfeltételek érvényesek.
    * **Interaktív hitelesítés** – Az Azure Active Directoryban létre kell hoznia egy **natív ügyfélalkalmazást**. Miután létrehozta az alkalmazást, kérje le az alkalmazáshoz kapcsolódó alábbi értékeket.
        - Az **ügyfél-azonosító** és az **átirányítási URI** lekérése az alkalmazáshoz
        - Delegált engedélyek beállítása

    * **Nem interaktív hitelesítés** (a cikk ezt használja) – Az Azure Active Directoryban létre kell hoznia egy **webalkalmazást**. Miután létrehozta az alkalmazást, kérje le az alkalmazáshoz kapcsolódó alábbi értékeket.
        - Az **ügyfél-azonosító**, az **ügyfél titkos kulcsa** és az **átirányítási URI** lekérése az alkalmazáshoz
        - Delegált engedélyek beállítása
        - Rendelje hozzá az Azure Active Directory-alkalmazást egy szerepkörhöz. A szerepkör lehet annak a hatókörnek a szintjén, amelyen engedélyt kíván biztosítani az Azure Active Directory-alkalmazásnak. Az alkalmazás például hozzárendelhető az előfizetés szintjén vagy egy erőforráscsoport szintjén is. 

    Az értékek lekérésére, az engedélyek beállítására és a szerepkörök hozzárendelésére vonatkozó utasítások: [Create Active Directory application and service principal using portal](../resource-group-create-service-principal-portal.md) (Active Directory-alkalmazás és egyszerű szolgáltatás létrehozása a portál használatával).

## Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?

Az alábbi kódrészlet a **nem interaktív** hitelesítéshez biztosít kódot, amely során az alkalmazás maga biztosítja saját hitelesítő adatait.

Ahhoz, hogy az oktatóanyag működjön, engedélyeznie kell az alkalmazásnak, hogy erőforrásokat hozzon létre az Azure-ban. **Határozottan ajánlott**, hogy a jelen oktatóanyagban csak közreműködői jogosultságokat adjon az alkalmazásnak egy új, nem használt és üres erőforráscsoporthoz az Azure-előfizetésben.

## Java-alkalmazás létrehozása

1. Nyissa meg az IntelliJ-t, és hozzon létre egy Java-projektet a **Parancssori alkalmazás** sablon használatával. Fejezze be a varázslót a projekt létrehozásához.

2. Kattintson a jobb gombbal a képernyő bal oldalán található projektre, majd kattintson az **Add Framework Support** (Keretrendszer-támogatás felvétele) elemre. Válassza a **Maven** lehetőséget, majd kattintson az **OK** gombra.

3. Nyissa meg az újonnan létrehozott **„pom.xml”** fájlt, majd illessze be a következő szövegrészletet a **\</version>** és a **\</project>** címkék közé:

    >[AZURE.NOTE] Ez a lépés ideiglenes, amíg az Azure Data Lake Store SDK elérhető nem lesz a Mavenben. Amikor az SDK elérhető válik a Mavenben, a cikket frissítjük. Az SDK minden jövőbeni frissítése elérhető lesz a Mavenen keresztül.

        <repositories>
            <repository>
                <id>adx-snapshots</id>
                <name>Azure ADX Snapshots</name>
                <url>http://adxsnapshots.azurewebsites.net/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                </snapshots>
            </repository>
            <repository>
                <id>oss-snapshots</id>
                <name>Open Source Snapshots</name>
                <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                <layout>default</layout>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <dependencies>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-client-authentication</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.azure</groupId>
                <artifactId>azure-mgmt-datalake-store</artifactId>
                <version>1.0.0-SNAPSHOT</version>
            </dependency>
        </dependencies>


4. Lépjen a **Fájl**, **Beállítások**, **Összeállítás, végrehajtás és üzembe helyezés** elemre. Bontsa ki a **Build Tools** (Összeállítási eszközök), **Maven**, és végül az **Importing** (Importálás) elemet. Jelölje be az **Import Maven projects automatically** (Maven-projektek automatikus importálása) jelölőnégyzetet. Kattintson az **Apply** (Alkalmaz), majd az **OK** gombra.

5. A bal oldali ablaktáblában lépjen az **src**, **main**, **java**, **\<csomag neve>** elemre, majd nyissa meg a **Main.java** fájlt, és cserélje le a meglévő kódblokkot az alábbi kódra. Továbbá adja meg a kódrészletben kiemelt paraméterek (pl. **localFolderPath**, **_adlsAccountName**, **_resourceGroupName**) értékeit , és cserélje le a következők helyőrzőit: **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID**, and **SUBSCRIPTION-ID**.

    A kód létrehozza a Data Lake Store-fiókot, a fájlokat a tárolóban, összefűzi és letölti a fájlokat, és végül törli a fiókot.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Futtassa az alkalmazást. Kövesse az utasításokat az alkalmazás futtatásához és befejezéséhez.

## További lépések

- [Secure data in Data Lake Store (Az adatok védelme a Data Lake Store-ban)](data-lake-store-secure-data.md)
- [Use Azure Data Lake Analytics with Data Lake Store (Az Azure Data Lake Analytics használata a Data Lake Store-ral)](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store (Az Azure HDInsight használata a Data Lake Store-ral)](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


