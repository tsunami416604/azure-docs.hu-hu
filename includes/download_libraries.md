## Azure Client Libraries for Java - 手動安裝

Azure Libraries for Java 分散在 [Apache 授權，2.0 版][license]。 按一下 [ [這裡][zip-download] 取得程式庫及所有相依性的 ZIP 檔案。  這可由 Microsoft Open Technologies，Inc. 請參閱 license.txt 和 ThirdPartyNotices.txt ZIP 內的檔案檔案授權及其他資訊。

## Azure Libraries for Java - Maven

如果您的專案已設定為使用 Maven 來建置，請將下列相依性新增至 pom.xml 檔案。 注意 ︰ 在使用 Azure libraries for Java 的 Eclipse 中建立 Maven 專案的詳細資訊，請參閱 [開始使用 Java 的 Azure 管理庫][maven-getting-started]。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>0.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>0.6.0</version>
    </dependency>


內 `<version>` 項目，在此範例中的版本號碼取代有效的版本號碼，您可以從取得 [Azure Libraries Repository on Maven](http://go.microsoft.com/fwlink/?LinkID=286274)。

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

