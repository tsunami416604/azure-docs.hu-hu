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
   ms.date="08/18/2016"
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

2. Nyissa meg a **File** (Fájl)  ->  **Project Structure** (Projektstruktúra)  ->  **Modules** (Modulok) (a Project Settings (Projektbeállítások) alatt) -> **Dependencies** (Függőségek)  ->  **+** -> **Library** (Könyvtár)  -> **From Maven** (A Mavenből) elemet.

3. Keressen rá a következő Maven-csomagokra, és adja hozzá őket a projektjéhez:

    * com.microsoft.azure:azure-mgmt-datalake-store:1.0.0-beta1.2
    * com.microsoft.azure:azure-mgmt-datalake-store-uploader:1.0.0-beta1.2
    * com.microsoft.azure:azure-client-authentication:1.0.0-beta2

4. A bal oldali ablaktáblában lépjen az **src**, **main**, **java**, **\<csomag neve>** elemre, majd nyissa meg a **Main.java** fájlt, és cserélje le a meglévő kódblokkot az alábbi kódra. Továbbá adja meg a kódrészletben kiemelt paraméterek (például **localFolderPath**, **DATA-LAKE-STORE-NAME**, **RESOURCE-GROUP-NAME**) értékeit, és cserélje le a **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** és **SUBSCRIPTION-ID** helyőrzőt az előfizetésére és az ahhoz tartozó Azure Active Directoryra vonatkozó adatokkal. További információk ezen adatok megtalálásáról: [az Azure útmutatója egyszerű szolgáltatások létrehozásához](../resource-group-authenticate-service-principal.md).

    A kód létrehozza a Data Lake Store-fiókot, a fájlokat a tárolóban, összefűzi és letölti a fájlokat, és végül törli a fiókot.

        package com.company;

        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreAccountManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.implementation.DataLakeStoreFileSystemManagementClientImpl;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.azure.management.datalake.store.uploader.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;

        public class Main {
            final static String ADLS_ACCOUNT_NAME = <DATA-LAKE-STORE-NAME>;
            final static String RESOURCE_GROUP_NAME = "<RESOURCE-GROUP-NAME>";
            final static String LOCATION = "East US 2";
            final static String TENANT_ID = "<TENANT-ID>";
            final static String SUBSCRIPTION_ID =  "<SUBSCRIPTION-ID>";
            final static String CLIENT_ID = "<CLIENT-ID>";
            final static String CLIENT_SECRET = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.

            private static DataLakeStoreAccountManagementClientImpl _adlsClient;
            private static DataLakeStoreFileSystemManagementClientImpl _adlsFileSystemClient;

            public static void main(String[] args) throws Exception {
                String localFolderPath = "C:\\local_path\\"; // TODO: Change this to any unused, new, empty folder on your local machine.

                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(CLIENT_ID, TENANT_ID, CLIENT_SECRET, null);
                SetupClients(creds);

                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");

                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", SUBSCRIPTION_ID));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.accounts().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.name());
                }
                WaitForNewline("Account(s) displayed.", "Uploading file.");

                // Upload a file to Data Lake Store: file1.csv
                UploadFile(localFolderPath + "file1.csv", "/file1.csv");
                WaitForNewline("File uploaded.", "Appending newline.");

                // Append newline to file1.csv
                AppendToFile("/file1.csv", "\r\n");
                WaitForNewline("Newline appended.", "Creating file.");

                // Create a new file in Data Lake Store: file2.csv
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("File created.", "Concatenating files.");

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
                _adlsClient.withSubscriptionId(SUBSCRIPTION_ID);
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
                adlsParameters.withLocation(LOCATION);

                _adlsClient.accounts().create(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME, adlsParameters);
            }

            // Create file
            public static void CreateFile(String path) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path);
            }

            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().create(ADLS_ACCOUNT_NAME, path, bytesContents, force);
            }

            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, AdlsErrorException {
                byte[] bytesContents = contents.getBytes();

                _adlsFileSystemClient.fileSystems().append(ADLS_ACCOUNT_NAME, path, bytesContents);
            }

            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().concat(ADLS_ACCOUNT_NAME, destFilePath, srcFilePaths);
            }

            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, AdlsErrorException {
                _adlsFileSystemClient.fileSystems().delete(ADLS_ACCOUNT_NAME, filePath);
            }

            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().getFileStatus(ADLS_ACCOUNT_NAME, path).getBody().fileStatus();
            }

            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, AdlsErrorException {
                return _adlsFileSystemClient.fileSystems().listFileStatus(ADLS_ACCOUNT_NAME, directoryPath).getBody().fileStatuses().fileStatus();
            }

            // Upload file
            public static void UploadFile(String srcPath, String destPath) throws Exception {
                UploadParameters parameters = new UploadParameters(srcPath, destPath, ADLS_ACCOUNT_NAME);
                FrontEndAdapter frontend = new DataLakeStoreFrontEndAdapterImpl(ADLS_ACCOUNT_NAME, _adlsFileSystemClient);
                DataLakeStoreUploader uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.execute();
            }

            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, AdlsErrorException {
                InputStream stream = _adlsFileSystemClient.fileSystems().open(ADLS_ACCOUNT_NAME, srcPath).getBody();

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
                _adlsClient.accounts().delete(RESOURCE_GROUP_NAME, ADLS_ACCOUNT_NAME);
            }
        }


6. Futtassa az alkalmazást. Kövesse az utasításokat az alkalmazás futtatásához és befejezéséhez.

## További lépések

- [Secure data in Data Lake Store (Az adatok védelme a Data Lake Store-ban)](data-lake-store-secure-data.md)
- [Use Azure Data Lake Analytics with Data Lake Store (Az Azure Data Lake Analytics használata a Data Lake Store-ral)](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Use Azure HDInsight with Data Lake Store (Az Azure HDInsight használata a Data Lake Store-ral)](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=sep16_HO1-->


