---
title: Biztonságos webalkalmazás fejlesztése | Microsoft Docs
description: Ez az egyszerű minta alkalmazás olyan ajánlott biztonsági eljárásokat valósít meg, amelyek javítják az alkalmazást és a szervezete biztonsági helyzetét az Azure-ban való fejlesztés során.
keywords: Na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 0683c065285a6ddf8d966bbd3d22e88c39b34d5c
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728803"
---
# <a name="develop-a-secure-web-app"></a>Biztonságos webalkalmazás fejlesztése

Ez a minta egy egyszerű Python-alkalmazás, amely a biztonsági erőforrásokra mutató hivatkozásokat tartalmazó weblapot jelenít meg az Azure-beli alkalmazások fejlesztéséhez. Az alkalmazás olyan biztonsági eljárásokat valósít meg, amelyek segíthetnek az alkalmazás és a szervezet biztonsági helyzetének javításában az Azure-beli alkalmazások fejlesztésekor.

Az alkalmazás-összetevők megfelelő konfigurálásának biztosítása érdekében kövesse a cikkben ismertetett lépéseket. Az adatbázis, Azure App Service, Azure Key Vault példány és az Azure Application Gateway példánya egymástól függ.

Az üzembe helyezési parancsfájlok beállítják az infrastruktúrát. Az üzembe helyezési parancsfájlok futtatása után az összetevők és szolgáltatások összekapcsolásához meg kell adnia néhány manuális konfigurációt a Azure Portalban.

A minta alkalmazás az Azure-ban olyan alkalmazások fejlesztését célozza meg, akik biztonsági mértékeket szeretnének megvalósítani az alkalmazásaikban.

Az alkalmazás fejlesztése és üzembe helyezése során megismerheti a következőket:

- Hozzon létre egy Azure Key Vault példányt, tárolja és kérje le a titkokat.
- Azure Database for PostgreSQL üzembe helyezése, biztonságos jelszavak beállítása és hozzáférés engedélyezése.
- Futtasson egy alpesi Linux-tárolót az Azure Web Apps for Linux platformon, és engedélyezze az Azure-erőforrások felügyelt identitásait.
- Hozzon létre és konfiguráljon egy Azure Application Gateway példányt egy olyan tűzfallal, amely az [OWASP Top 10 szabályrendszert](https://coreruleset.org/)használja.
- Az Azure-szolgáltatások használatával engedélyezheti az átvitelben és a nyugalmában lévő adatok titkosítását.

Az alkalmazás fejlesztése és üzembe helyezése után be kell állítania az alábbi minta-webalkalmazást, valamint a konfigurációs és biztonsági mértékeket.

![Minta webalkalmazás](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architektúra
Az alkalmazás egy tipikus n szintű alkalmazás három réteggel. Itt látható az előtér-, a háttér-és az adatbázis-réteg az integrált figyelési és titkos felügyeleti összetevőkkel:

![Alkalmazásarchitektúra](./media/secure-web-app/architecture.png)

Az architektúra az alábbi összetevőkből áll:

- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Átjárót és tűzfalat biztosít az alkalmazás architektúrája számára.
- [Azure Web Apps Linuxon](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro). A tároló futtatókörnyezetét biztosítja a Python-alkalmazás Linux-környezetben való futtatásához.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Tárolja és titkosítja az alkalmazás titkait, és felügyeli a rájuk épülő hozzáférési szabályzatok létrehozását.
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). Biztonságosan tárolja az alkalmazás adatfájljait.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/) és az [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Monitorozást és riasztásokat biztosít az alkalmazás működéséről.

## <a name="threat-model"></a>Veszélyforrások modellje
A veszélyforrások modellezése a potenciális biztonsági fenyegetések azonosításának folyamata a vállalat és az alkalmazás számára, majd a megfelelő kockázatcsökkentő terv biztosítása.

Ez a példa a [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) használta a biztonságos minta alkalmazás veszélyforrások modellezésének megvalósítására. Az összetevők és az adatfolyamatok diagramon való kiépítésével a fejlesztési folyamat elején azonosíthatja a problémákat és a fenyegetéseket. Ez később időt és pénzt takarít meg.

Ez a minta alkalmazáshoz tartozó veszélyforrás-modell:

![Veszélyforrások modellje](./media/secure-web-app/threat-model.png)

A veszélyforrások modellezése eszköz által generált veszélyforrások és potenciális sebezhetőségek a következő képernyőképen jelennek meg. A fenyegetés modell áttekintést nyújt a feltett támadási felületről, és felszólítja a fejlesztőket a problémák enyhítésére.

![A veszélyforrások modell kimenete](./media/secure-web-app/threat-model-output.png)

Az előző veszélyforrás-modell kimenetének SQL-injektálását például a felhasználói bevitelek tisztításával és a Azure Database for PostgreSQL tárolt funkcióinak használatával csökkenthetők. Ez a megoldás megakadályozza a lekérdezések tetszőleges végrehajtását az adatolvasások és írások során.

A fejlesztők javítják a rendszerek általános biztonságát azáltal, hogy elhárítják az egyes fenyegetéseket a veszélyforrások modelljének kimenetében.

## <a name="deployment"></a>Környezet
A következő lehetőségek lehetővé teszik a Linux futtatását Azure App Serviceon:

- Válasszon egy tárolót az Azure-beli előre összeállított Microsoft-tárolók listájából, amelyek támogató technológiákkal lettek létrehozva (Python, Ruby, PHP, Java, Node. js, .NET Core).
- Egyéni kialakítású tároló használata. Válassza ki a saját tároló-nyilvántartásokat a rendszerkép forrásaként, és építsen a HTTP-t támogató számos elérhető technológiára.

Ebben a példában azt a telepítési parancsfájlt fogja futtatni, amely telepíti a webappot App Service és létrehozza az erőforrásokat.

Az alkalmazás az alább látható különböző üzembe helyezési modelleket használhatja:

![Üzembe helyezési adatfolyam diagramja](./media/secure-web-app/deployment.png)

Az Azure-ban számos módon telepíthet alkalmazásokat, beleértve a következőket:

- Azure Resource Manager-sablonok
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

Ez az alkalmazás használatban van:

- [](https://docs.docker.com/) A Docker létrehozza és felépíti a tároló lemezképeit.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) üzembe helyezéshez.
- A [Docker hub](https://hub.docker.com/) tároló-beállításjegyzékként.

## <a name="security-considerations"></a>Biztonsági szempontok

### <a name="network"></a>Network (Hálózat)
A minta alkalmazás végpontok közötti SSL-titkosítást használ a hálózatra irányuló és onnan kimenő forgalomhoz. Az átjáró önaláírt tanúsítvánnyal van konfigurálva.
> [!IMPORTANT]
> Ebben a bemutatóban egy önaláírt tanúsítványt használunk. Éles környezetben egy ellenőrzött hitelesítésszolgáltatótól (CA) kell beszerezni a tanúsítványokat.

Az alkalmazás tűzfala emellett megvizsgálja a bejövő forgalmat és a riasztásokat a rendszergazdáknál, ha a hálózati forgalomban kártékony forgalom észlelhető.
Application Gateway csökkenti a fenyegetések modellben felderített DDoS-és SQL-befecskendezési fenyegetések lehetőségét.

### <a name="identity"></a>Identitás
A portálra való bejelentkezéshez a minta alkalmazás többtényezős hitelesítést használ Azure Active Directory (Azure AD) rendszergazdák számára, akik hozzá vannak rendelve az erőforrásokhoz.
A minta alkalmazás felügyelt identitásokat használ a titkos kulcsok olvasására és beolvasására a Azure Key Vaultból, így biztosítva, hogy az alkalmazásnak ne kelljen a titkos kódokhoz tartozó hitelesítő adatokkal és tokenekkel elolvasnia a titkokat. Az Azure AD automatikusan létrehozza azokat az egyszerű szolgáltatásokat, amelyeket az alkalmazásnak a felügyelt identitások használatakor el kell olvasnia és módosítania kell a titkokat.

Az Azure-erőforrások és az MFA felügyelt identitásai révén a támadók megnehezítik a jogosultságok megszerzését és a jogosultságuk kiterjesztését a rendszeren. Ez a fenyegetés a fenyegetés modellben lett kimutatva.
Az alkalmazás az OAuth-t használja, amely lehetővé teszi, hogy a OAuth alkalmazásban regisztrált felhasználók bejelentkezzenek az alkalmazásba.

### <a name="storage"></a>Storage
A PostgreSQL-adatbázisban lévő adatok Azure Database for PostgreSQL automatikusan titkosítva maradnak a nyugalmi állapotban. Az adatbázis engedélyezi a App Service IP-címeket, így csak a telepített App Service webalkalmazás férhet hozzá az adatbázis-erőforrásokhoz a megfelelő hitelesítő adatokkal.

### <a name="logging-and-auditing"></a>Naplózás és naplózás
Az alkalmazás végrehajtja a naplózást Application Insights használatával követheti a metrikákat, a naplókat és a kivételeket. Ez a naplózás elegendő alkalmazás-metaadatokat biztosít ahhoz, hogy tájékoztassák a fejlesztőket és az operatív csapat tagjait az alkalmazás állapotáról. Emellett a biztonsági incidensek esetén is elegendő mennyiségű adattal rendelkezik.

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok
Ha még nem rendelkezik Azure-fiókkal, akkor létrehozhat egy ingyenes fiókot. A kezdéshez nyissa meg az [ingyenes fiók lapot](https://azure.microsoft.com/free/) , és tekintse meg, hogy mit tehet egy ingyenes Azure-fiókkal, és hogy mely termékek ingyenesek 12 hónapig.

Ha a minta alkalmazásban lévő erőforrásokat a biztonsági funkciókkal szeretné üzembe helyezni, a prémium funkciókért kell fizetnie. Az alkalmazások skálázása, valamint az Azure által kínált ingyenes csomagok és kísérletek frissítése az alkalmazásokra vonatkozó követelmények kielégítése érdekében várható, hogy a költségek növekednek. A költségek becsléséhez használja az Azure [díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/) .

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése
### <a name="prerequisites"></a>Előfeltételek
Az alkalmazás működésének megkezdéséhez telepítenie kell az alábbi eszközöket:

- Az alkalmazás kódjának módosítására és megtekintésére szolgáló Kódszerkesztő. A [Visual Studio Code](https://code.visualstudio.com/) egy nyílt forráskódú lehetőség.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) a fejlesztői számítógépen.
- [Git](https://git-scm.com/) a rendszeren. A git a forráskód helyi klónozására szolgál.
- [jQ](https://stedolan.github.io/jq/), egy UNIX-eszköz, amely felhasználóbarát módon kérdezi le a JSON-t.

A minta alkalmazás erőforrásainak üzembe helyezéséhez Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a minta alkalmazás teszteléséhez.

Az eszközök telepítése után már készen áll az alkalmazás üzembe helyezésére az Azure-ban.

### <a name="environment-setup"></a>A környezet beállítása
Futtassa a telepítési parancsfájlokat a környezet és az előfizetés beállításához:

1. A forráskód-tárház klónozásához használja ezt a git-parancsot:

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. A könyvtárba való áthelyezéshez használja a következő parancsot:

   ```
   cd tutorial-project/scripts
   ```

3. A parancsfájlok mappában vannak olyan fájlok, amelyek az Ön által használt platformra (Windows vagy Linux) vonatkoznak. Mivel az Azure CLI már telepítve van, jelentkezzen be az Azure-fiókba a parancssorban ehhez a CLI-parancs futtatásával:

   ``` azurecli
   az login
   ```

Ekkor megnyílik a böngésző, és jelentkezzen be a hitelesítő adataival. A bejelentkezést követően megkezdheti az erőforrások telepítését a parancssorból.

A telepítési parancsfájlok `deploy-powershell.ps1` és `deploy-bash.sh` a teljes alkalmazást telepítő programkódot tartalmaznak.
A megoldás üzembe helyezése:

1. Ha a PowerShell-t futtatja `deploy-powershell.ps1` a fájl futtatásával, írja `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` be a régió és az erőforráscsoport nevének lecserélése megfelelő Azure-régiókkal és az erőforráscsoport nevét.
2. Ha Linux rendszeren futtatja a `deploy-bash.sh` fájlt `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`, akkor előfordulhat, hogy beírja a fájl végrehajtható fájlját`chmod +x deploy-bash.sh`

Az alábbi példák a legfontosabb összetevők töredékeit mutatják be. A példákat egyenként vagy a többi összetevővel is üzembe helyezheti a fájlok telepítése lehetőség futtatásával.

### <a name="implementation-guidance"></a>Implementálási segédlet
Az üzembe helyezési parancsfájl egy olyan parancsfájl, amely négy fázisra osztható fel. Mindegyik fázis üzembe helyezi és konfigurálja az [architektúra](#architecture)-diagramban található Azure-erőforrásokat.

A négy fázis a következők:

- Azure Key Vault üzembe helyezése.
- Azure Database for PostgreSQL üzembe helyezése.
- Azure-Web Apps üzembe helyezése Linuxon.
- Application Gateway üzembe helyezése a webalkalmazási tűzfallal.

Az egyes fázisok a korábban üzembe helyezett erőforrások konfigurációjának használatával épülnek fel az előzőre.

A megvalósítás lépéseinek elvégzéséhez győződjön meg arról, hogy telepítette az [Előfeltételek](#prerequisites)szakaszban felsorolt eszközöket.

#### <a name="deploy-azure-key-vault"></a>Azure Key Vault üzembe helyezése
Ebben a szakaszban egy Azure Key Vault-példányt hoz létre és helyez üzembe, amely a titkok és a tanúsítványok tárolására szolgál.

Az üzembe helyezés befejezése után az Azure-ban üzembe helyezhető egy Azure Key Vault példány.

Azure Key Vault üzembe helyezése az Azure CLI használatával:

1. Deklarálja Azure Key Vault változóit.
2. Regisztrálja a Azure Key Vault szolgáltatót.
3. Hozza létre a példányhoz tartozó erőforráscsoportot.
4. Hozza létre a Azure Key Vault példányt a 3. lépésben létrehozott erőforráscsoporthoz.

   ``` azurecli

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```
Ajánlott a felügyelt identitások használata az Azure-erőforrásokhoz olyan alkalmazásokban, amelyek a Key Vault használatával férnek hozzá az erőforrásokhoz. A biztonsági helyzet akkor nő, ha a hozzáférési kulcsok Key Vault nem a kódban vagy a konfigurációban vannak tárolva.

#### <a name="deploy-azure-database-for-postgresql"></a>Azure Database for PostgreSQL üzembe helyezése
Azure Database for PostgreSQL a következő módon működik: először hozza létre az adatbázis-kiszolgálót, majd hozza létre azt az adatbázist, amelybe a sémát és az adatfájlokat tárolni szeretné.

Az üzembe helyezés befejezése után egy PostgreSQL-kiszolgáló és egy Azure-beli adatbázis fut.

Azure Database for PostgreSQL üzembe helyezése az Azure CLI használatával:

1. Nyisson meg egy terminált az Azure CLI-vel és az Azure-előfizetés beállításával.
2. Az adatbázis eléréséhez használt biztonságos Felhasználónév és jelszó kombinációjának létrehozása. (Ezeket a Azure Key Vault az azokat használó alkalmazások számára kell tárolni.)
3. Hozza létre a PostgreSQL-kiszolgáló példányát.
4. Hozzon létre egy adatbázist a 3. lépésben létrehozott kiszolgálópéldány alapján.
5. Futtassa a PostgreSQL-szkripteket a PostgreSQL-példányon.

Az alábbi kód az Azure kulcstartóban tárolt PGUSERNAME és PGPASSWORD-titkokra támaszkodik a fenti kulcstartó üzembe helyezése lépésből.

   ``` azurecli
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Az adatbázis központi telepítése után a hitelesítő adatait és a kapcsolati karakterláncot a Azure Key Vaultban kell tárolnia.
A parancsfájlok mappában található egy `functions.sql` fájl, amely tartalmazza azt a com/pgSQL kódot, amely a futtatott függvényeket hozza létre a futtatásakor. A fájl futtatása a felparaméterezi az SQL-injektálás korlátozására.

A PostgreSQL egy nevű `psql` eszközzel van ellátva, amely az adatbázishoz való kapcsolódásra szolgál. A futtatásához `functions.sql`csatlakoznia kell a Azure Database for PostgreSQL-példányhoz a helyi gépről, és onnan kell futtatnia. A psql eszköz telepítése a PostgreSQL alapértelmezett telepítése minden operációs rendszeren megtalálható.
További információkért tekintse meg a [Psql dokumentációját](https://www.postgresql.org/docs/9.3/app-psql.html).

A Azure Cloud shell az `psql` eszközt is tartalmazza. A Azure Portal Cloud Shell közvetlenül a Cloud Shell ikonra kattintva is használhatja.

A PostgreSQL-példány távoli elérésének engedélyezéséhez engedélyeznie kell az IP-címet a PostgreSQL-ben.
A hozzáférés engedélyezéséhez lépjen a **kapcsolatbiztonsági** lapra, válassza az **ügyfél IP-** címének hozzáadása lehetőséget, és mentse az új beállításokat.

![Ügyfél IP-címének engedélyezése](./media/secure-web-app/add-client-ip-postgres.png)

Ha a helyi psql eszköz helyett Cloud Shell használ, válassza az Azure- **szolgáltatásokhoz való hozzáférés engedélyezése** lehetőséget, és módosítsa az értékét **a on** értékre, hogy engedélyezze a Cloud shell-hozzáférést.

Ezután kapcsolódjon a példányhoz az alábbi psql parancs kapcsolati karakterlánc-paraméterekkel való futtatásával a Azure Portal található PostgreSQL-példány kapcsolati karakterláncok lapján.
Cserélje le az üres kapcsos zárójeleket az adatbázis kapcsolatok karakterlánc paneljéről, valamint a jelszót a Azure Key Vault.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

A következő com/pgSQL parancsfájl futtatásával ellenőrizze, hogy csatlakozik-e az adatbázishoz. A szkript létrehozza az adatok adatbázisba való beszúrásához használt tárolt függvényeket.

```sql
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;


CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```


Az SSL és a hitelesítésszolgáltató (CA) PostgreSQL-ellenőrzésének beállításával kapcsolatos további információkért lásd: [az SSL-kapcsolat konfigurálása a Azure Database for PostgreSQLban](https://docs.microsoft.com/en-us/azure/postgresql/concepts-ssl-connection-security).

A tároló főtanúsítványt tartalmaz. A tanúsítvány beszerzésének lépései a következők:

1. Töltse le a tanúsítványt a hitelesítésszolgáltatótól [.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. [Töltse le és telepítse az OpenSSL-](https://docs.microsoft.com/en-us/azure/postgresql/concepts-ssl-connection-security)t a gépén.
3. A tanúsítványfájl dekódolása:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

További információ az SSL-biztonság konfigurálása a PostgreSQL-hez itt az [SSL-kapcsolat biztonságának konfigurálása](https://docs.microsoft.com/en-gb/azure/postgresql/concepts-ssl-connection-security)című cikk nyújt tájékoztatást.

#### <a name="deploy-azure-web-apps-on-linux"></a>Azure-Web Apps üzembe helyezése Linux rendszeren
A Linux-szolgáltatásokat egyszerűen hozhatja létre Azure App Service az Azure-ban a széles körben használt nyelvekhez, például a Pythonhoz, a Rubyhez és C#a Javához készült előre összeállított tárolók és képek készlete is elérhető. Az Azure támogatja az egyéni tárolókat is, amelyek lehetővé teszik, hogy gyakorlatilag minden programozási nyelv fusson a Azure App Service platformon.

Az üzembe helyezett alkalmazás egy egyszerű Python-alkalmazás, amely a legújabb Ubuntu Linux disztribúción fut. A szolgáltatás az előző szakaszban létrehozott Azure Key Vault és PostgreSQL-példányokhoz kapcsolódik a hitelesítő adatok kezeléséhez és az adattároláshoz.

Az alkalmazás gyökérkönyvtárában a következő Docker-fájl található:

``` docker
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

A fenti Docker a Azure Container Registryon `mcr.microsoft.com/samples/basic-linux-app`tárolt tároló összeállítására szolgál.

Az alábbi kód:

1. Deklarálja a App Service példány változóit és nevét.
2. Létrehoz egy erőforráscsoportot a App Service tervhez.
3. Azure-Web Apps létrehozása Linux-tárolók példányain.
4. Engedélyezi a Web App-tároló naplózását.
5. Beállítja az alkalmazások konfigurációját a tároló Alkalmazásbeállítások között.

   ```
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }

   ```

Ez a szkript létrehoz egy hozzárendelt identitást az App Service-példányhoz, amelyet az MSI használatával használhat a kód vagy konfiguráció során felhasználható kódolási titkok nélküli Azure Key Vault.

Lépjen a portál Azure Key Vault példányára, hogy engedélyezze a hozzárendelt identitást a hozzáférési szabályzat lapon. Válassza az **új hozzáférési házirend hozzáadása**lehetőséget. A **rendszerbiztonsági tag kiválasztása**területen keresse meg az alkalmazás nevét, amely hasonló a létrehozott app Service példány nevéhez.
Az alkalmazáshoz csatolt egyszerű szolgáltatásnak láthatónak kell lennie. Válassza ki, és mentse a hozzáférési szabályzat lapot, ahogy az alábbi képernyőképen is látható.

Mivel az alkalmazásnak csak a kulcsokat kell lekérnie, a titkok lehetőségnél válassza ki a **Get** engedélyt, amely lehetővé teszi a hozzáférést a megadott jogosultságok csökkentése mellett.

![Hozzáférési szabályzat Key Vault](./media/secure-web-app/kv-access-policy.png)

*Key Vault hozzáférési szabályzat létrehozása*

Mentse a hozzáférési házirendet, majd mentse az új módosítást a **hozzáférési házirendek** lapon a házirendek frissítéséhez.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Application Gateway üzembe helyezése a webalkalmazási tűzfallal engedélyezve
A Web Apps szolgáltatásban nem javasoljuk, hogy közvetlenül az interneten keresztül tegye elérhetővé a szolgáltatásokat.
A terheléselosztás és a tűzfalszabályok nagyobb biztonságot és szabályozást biztosítanak a bejövő forgalom számára, és segítenek a felügyeletben.

Application Gateway példány üzembe helyezése:

1. Hozzon létre egy erőforráscsoportot az Application Gateway kibontásához.
2. Hozzon létre egy virtuális hálózatot az átjáróhoz való csatlakoztatáshoz.
3. Hozzon létre egy alhálózatot az átjáróhoz a virtuális hálózaton.
4. Nyilvános IP-cím kiépítése.
5. Az Application Gateway kiépítése.
6. Engedélyezze a webalkalmazási tűzfalat az átjárón.

   ``` azurecli
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Az előző szkript:

1. Létrehoz egy új önaláírt tanúsítványt az Azure-ban.
2. Letölti az önaláírt tanúsítványt Base64 kódolású fájlként.
3. Jelszót hoz létre az önaláírt tanúsítványhoz.
4. Exportálja a tanúsítványt a jelszóval aláírt PFX-fájlként.
5. A tanúsítvány jelszavát a Azure Key Vault tárolja.

Ez a szakasz az Application Gateway üzembe helyezését végzi el:

```powershell
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Az üzembe helyezés befejezése után egy webalkalmazási tűzfallal rendelkező Application Gateway van engedélyezve.

Az átjáró-példány a HTTPS 443-es portját teszi elérhetővé. Ez a konfiguráció biztosítja, hogy az alkalmazás csak a 443-es porton keresztül legyen elérhető a HTTPS protokollon keresztül.

A nem használt portok blokkolása és a támadási felületek expozíciójának korlátozása az ajánlott biztonsági eljárás.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Hálózati biztonsági csoportok hozzáadása az App Service-példányhoz

App Service példányok integrálható a virtuális hálózatokkal. Ez az integráció lehetővé teszi, hogy az alkalmazás bejövő és kimenő forgalmát kezelő hálózati biztonsági csoportházirend-házirendekkel konfigurálják őket.

1. A szolgáltatás engedélyezéséhez az Azure app Service-példány panel **Beállítások**területén válassza a **hálózatkezelés**lehetőséget. A jobb oldali ablaktábla VNet- **integráció**területén válassza a **konfigurálásához kattintson ide**.

   ![Új virtuális hálózat integrációja](./media/secure-web-app/app-vnet-menu.png)

    *Új Virtual Network Integration for App Service*
1. A következő lapon válassza a **VNET hozzáadása (előzetes verzió)** lehetőséget.

1. A következő menüben válassza ki azt a virtuális hálózatot, amely a (z `hello-vnet`) rendszerű központi telepítésben lett létrehozva. Létrehozhat egy új alhálózatot, vagy kijelölhet egy meglévőt is.
   Ebben az esetben hozzon létre egy új alhálózatot. Állítsa a **címtartományt** a **10.0.3.0/24** értékre, és nevezze el az alhálózati **alkalmazás-** alhálózatot.

   ![App Service virtuális hálózati konfiguráció](./media/secure-web-app/app-vnet-config.png)

    *App Service virtuális hálózati konfigurációja*

Most, hogy engedélyezte a virtuális hálózat integrációját, hozzáadhat hálózati biztonsági csoportokat az alkalmazáshoz.

1. A keresőmező segítségével keressen **hálózati biztonsági csoportokat**. Az eredmények között válassza a **hálózati biztonsági csoportok** lehetőséget.

    ![Hálózati biztonsági csoportok keresése](./media/secure-web-app/nsg-search-menu.png)

    *Hálózati biztonsági csoportok keresése*

2. A következő menüben válassza a **Hozzáadás**lehetőséget. Adja meg a NSG **nevét** és azt az **erőforráscsoportot** , amelyben a helye található. Ez a NSG az Application Gateway alhálózatára lesz alkalmazva.

    ![NSG létrehozása](./media/secure-web-app/nsg-create-new.png)

    *NSG létrehozása*

3. A NSG létrehozása után válassza ki azt. A paneljén a **Beállítások**területen válassza a **bejövő biztonsági szabályok**elemet. Konfigurálja ezeket a beállításokat, hogy engedélyezze az Application Gateway felé irányuló kapcsolatokat az 443-as porton keresztül.

   ![A NSG konfigurálása](./media/secure-web-app/nsg-gateway-config.png)

   *A NSG konfigurálása*

4. Az átjáró NSG kimenő szabályaiban adjon hozzá egy olyan szabályt, amely engedélyezi a kimenő kapcsolatokat a App Service-példányhoz egy olyan szabály létrehozásával, amely `AppService`a szolgáltatási címkét célozza meg:

   ![Kimenő szabályok hozzáadása a NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Kimenő szabályok hozzáadása a NSG*

    Adjon hozzá egy másik kimenő szabályt, amely lehetővé teszi, hogy az átjáró kimenő szabályokat küldjön egy virtuális hálózatra.

   ![Másik kimenő szabály hozzáadása](./media/secure-web-app/nsg-outbound-vnet.png)

    *Másik kimenő szabály hozzáadása*

5. A NSG alhálózatok paneljén válassza a **hozzárendelés**lehetőséget, válassza ki a központi telepítésben létrehozott virtuális hálózatot, és válassza ki a **GW-alhálózat**nevű átjáró-alhálózatot. A NSG az alhálózatra alkalmazza a rendszer.

6. Hozzon létre egy másik NSG, mint az előző lépésben, ezúttal a App Service példányra vonatkozóan. Adja meg a nevet. Adja hozzá a bejövő szabályt az 443-as porthoz, ahogy az Application Gateway-NSG tette.

   Ha App Service példánya van telepítve egy App Service Environment példányon, amely nem ebben az alkalmazásban, akkor a App Service NSG bejövő biztonsági csoportjain a 454-455-es port megnyitásával engedélyezheti a Azure Service Health-mintavételek számára a bejövő szabályok megadását. A konfiguráció a következő:

   ![Azure Service Health-mintavétel szabályainak hozzáadása](./media/secure-web-app/nsg-create-healthprobes.png)

    *Azure Service Health-mintavétel szabályainak hozzáadása (csak App Service Environment)*

7. A kimenő biztonsági szabályokban hozzon létre egy új kimenő biztonsági szabályt, amely lehetővé teszi, hogy az App Service-példány kommunikáljon a PostgreSQL-adatbázissal. Konfigurálja a következőhöz hasonló módon:

   ![A kimenő PostgreSQL-kapcsolatok engedélyezésére szolgáló szabály](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Szabály hozzáadása a kimenő PostgreSQL-kapcsolatok engedélyezéséhez*

A támadási felület korlátozásához módosítsa a App Service hálózati beállításokat úgy, hogy csak az Application Gateway hozzáférjen az alkalmazáshoz.
Ehhez lépjen a App Service hálózat lapra, válassza az **IP-korlátozások** lapot, és hozzon létre egy engedélyezési szabályt, amely lehetővé teszi, hogy csak az Application Gateway IP-címe legyen közvetlenül elérhető a szolgáltatáshoz.

Az átjáró IP-címét az Áttekintés lapjáról kérheti le. Az **IP-CIDR** lapon adja meg az IP-címet a következő formátumban: `<GATEWAY_IP_ADDRESS>/32`.

![Csak az átjáró engedélyezése](./media/secure-web-app/app-allow-gw-only.png)

*A App Service elérésének engedélyezése csak az átjáró IP-címéhez*


#### <a name="implement-azure-active-directory-oauth"></a>Azure Active Directory OAuth megvalósítása

A minta webalkalmazás oldalon elosztott Azure-dokumentumok olyan erőforrások az alkalmazásban, amelyeknek szüksége lehet a védelemre. Az Azure Active Directory (Azure AD) használatával különböző hitelesítési folyamatokkal implementálhatja a webes, asztali és mobil alkalmazások hitelesítését.
Az alkalmazás a **Microsofttal**való bejelentkezést használja, amely lehetővé teszi az alkalmazás számára, hogy beolvassa az egybérlős Azure ad-felhasználó listájához hozzáadott felhasználók profilját.

A Azure Portal konfigurálja az alkalmazást a szükséges hitelesítő adatok használatára:

1. Válassza a **Azure Active Directory**lehetőséget, vagy keresse meg a keresést a keresőmező használatával.

2. **Új regisztráció**kiválasztása:

   ![Regisztráció létrehozása](./media/secure-web-app/ad-auth-create.png)

   *Azure AD-alkalmazás regisztrálásának létrehozása*

3. A következő lapon adja meg az alkalmazás nevét. A **támogatott fiókok típusai**területen **csak a szervezeti címtárban**válassza a fiókok elemet.
    Az **átirányítási URI**területen adja meg azt az alaptartományt, amelyen az alkalmazás fut, valamint egy a jogkivonat-végponttal. Példa: *GATEWAY_HASH*. cloudapp.net/token.

   ![Az Azure AD-alkalmazás regisztrálásának konfigurálása](./media/secure-web-app/ad-auth-type.png)

   *Az Azure AD-alkalmazás regisztrálásának konfigurálása*

4. Megjelenik egy képernyő, amely megjeleníti a regisztrált alkalmazást és annak adatait. Ezt az információt fel kell vennie az Azure Key Vault-példányba.
   1. Másolja át az alkalmazás (ügyfél) AZONOSÍTÓját, és mentse Key Vault `CLIENTID`néven.
   2. Másolja az előző lépésben megadott átirányítási URI-t `REDIRECTURI`, és mentse azt.
   3. Másolja az Azure AD alapértelmezett könyvtárának nevét, amelynek a formátuma a *Name*. microsoftonline.com, és mentse Key Vaultként `TENANT`.
   4. Nyissa meg a korábban létrehozott Azure AD-alkalmazás **tanúsítványok & titkok** lapját, és válassza az **új ügyfél titka**lehetőséget, ahogy az alábbi képernyőképen is látható. Állítsa be a lejárati dátumot, majd másolja a generált értéket, és mentse Key Vaultként `CLIENTSECRET`.

      ![Azure AD-engedélyezési titok](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD-engedélyezési titok*

   5. Biztonságos, véletlenszerű titkos kulcs létrehozása bármely parancssori/online eszköz használatával. Mentse Key Vaultként `FLASKSECRETKEY`. Az alkalmazás-keretrendszer ezt a kulcsot használja a munkamenetek létrehozásához.
        A titkos kulcsok létrehozásával kapcsolatos további információkért lásd: [lombik](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)-munkamenetek.

5. A bejelentkezés konfigurálása után fel kell vennie a felhasználókat az Azure AD-hivatkozásba, hogy be lehessen jelentkezni az erőforrásba. A hozzáadáshoz nyissa meg a **felhasználók** lapot az Azure ad-ban, válassza a **minden felhasználó**lehetőséget, majd válassza az **új felhasználó** vagy az **új vendég felhasználó**lehetőséget. Teszteléshez hozzáadhat egy vendég felhasználót, és meghívhatja a felhasználót a címtárba. Új felhasználót is hozzáadhat, ha az alkalmazást futtató tartomány érvényesítése megtörtént. Ebben a példában csak az Azure AD-bérlőben regisztrált felhasználók regisztrálhatók a hozzáféréshez. A több-bérlős bejelentkezések elérésével kapcsolatos információkért tekintse meg a dokumentációt.

   ![Felhasználók hozzáadása az alapértelmezett tartományhoz](./media/secure-web-app/ad-auth-add-user.png)

   *Felhasználók hozzáadása az alapértelmezett Azure Active Directory tartományhoz*

Miután hozzáadta az Azure AD-konfigurációt és a titkos kulcsokat a Key Vaulthoz, a felhasználók az Azure OAuth-hitelesítés használatával hitelesíthetők az alkalmazásba.
Az alkalmazás kódjában ezt a Azure Active Directory Authentication Library (ADAL) kezeli.

Miután a titkok bekerültek a Key Vaultba, és az alkalmazás hozzáfér a titkokhoz és az adatbázishoz, az Application Service elérhető az átjáró alkalmazásának URL https://GATEWAY_HASH.cloudapp.net) -címén keresztül (amely a panelről szerezhető be).

Ha az Azure AD-ba való bejelentkezéskor egy hibaüzenet jelenik meg, amely szerint a "felhasználó nincs regisztrálva abban a címtárban, amelyet be szeretne jelentkezni," fel kell vennie a felhasználót. A felhasználó hozzáadásához nyissa meg az Azure AD Users ( **felhasználók** ) lapját, és adja hozzá manuálisan a felhasználót a részletek beírásával vagy a felhasználó meghívásával, ha az e-mail-címét vendégként adja meg az Azure ad-ban a **vendég** meghívása panelen.

#### <a name="deploy-application-insights"></a>Application Insights üzembe helyezése
Most, hogy az alkalmazás telepítve van és működik, az alkalmazáson belüli hibákat kell kezelnie a naplózási és nyomkövetési adatgyűjtés mellett.
A naplózási és nyomkövetési adatgyűjtés az alkalmazásban megjelenő naplózási eseményeket jeleníti meg.

Application Insights egy olyan szolgáltatás, amely a felhasználók vagy a rendszer által létrehozható naplókat gyűjti.

Application Insights példány létrehozása:

1. A Azure Portal található keresőmező használatával keressen **Application Insights** .
2. Válassza a **Application Insights**lehetőséget. Adja meg az itt látható adatokat egy példány létrehozásához.

   ![Application Insights példány létrehozása](./media/secure-web-app/app-insights-data.png)

Az üzembe helyezés befejezése után Application Insights példánya van.

Miután létrehozta az Applications-elemzések példányát, meg kell adnia az alkalmazásnak a rendszerállapot-kulcsot, amely lehetővé teszi, hogy naplókat küldjön a felhőbe. Ezt úgy teheti meg, hogy beolvassa a Application Insights kulcsot, és az Azure által a Application Insights számára biztosított alkalmazás-tárakon belül használja. Az ajánlott eljárás az, hogy a kulcsokat és a titkokat a Azure Key Vault tárolja, hogy azok biztonságban maradjanak.

Az alapszintű minta alkalmazás esetében az Applications-elemzések példányának létrehozása után az alkalmazásnak ismernie kell a kialakítási kulcsot, amely lehetővé teszi, hogy a naplófájlokat küldjön a felhőbe.
A Key Vaultban állítson `APPINSIGHTSKEY` be egy titkos kulcsot, és állítsa be az értékét a kialakítási kulcsként. Ez lehetővé teszi, hogy az alkalmazás naplókat és mérőszámokat küldjön Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Többtényezős hitelesítés implementálása Azure Active Directory
A rendszergazdáknak biztosítaniuk kell, hogy a portálon lévő előfizetési fiókok védve legyenek. Az előfizetés sebezhető a támadásokkal szemben, mert kezeli a létrehozott erőforrásokat. Az előfizetés védetté tételéhez engedélyezze a többtényezős hitelesítést az előfizetés **Azure Active Directory** lapján.

Az Azure AD olyan szabályzatok alapján működik, amelyek egy adott feltételnek megfelelő felhasználóra vagy felhasználói csoportokra vonatkoznak.
Az Azure egy alapértelmezett szabályzatot hoz létre, amely megadja, hogy a rendszergazdáknak kétfaktoros hitelesítésre van szükségük a portálra való bejelentkezéshez.
A házirend engedélyezése után a rendszer felszólítja a kijelentkezésre, majd a Azure Portalba való bejelentkezésre.

Az MFA engedélyezése rendszergazdai bejelentkezésekhez:

1. Lépjen a **Azure Active Directory** lapra a Azure Portal
2. A biztonság kategóriában válassza a feltételes hozzáférés lehetőséget. Ezt a képernyőt látja:

   ![Feltételes hozzáférés – házirendek](./media/secure-web-app/ad-mfa-conditional-add.png)

Ha nem tud új szabályzatot létrehozni:

1. Nyissa meg az **MFA** lapot.
2. Válassza a prémium szintű Azure AD **ingyenes próbaverzió** hivatkozást az ingyenes próbaverzióra való előfizetéshez.

   ![prémium szintű Azure AD ingyenes próbaverzió](./media/secure-web-app/ad-trial-premium.png)

Térjen vissza a feltételes hozzáférés képernyőre.

1. Válassza az új házirend lapot.
2. Adja meg a házirend nevét.
3. Válassza ki azokat a felhasználókat vagy csoportokat, amelyeknek engedélyezni szeretné az MFA-t.
4. A **hozzáférés-vezérlés**területen válassza az **Engedélyezés** lapot, majd válassza a többtényezős **hitelesítés** megkövetelése (és egyéb beállítások) lehetőséget.

   ![MFA megkövetelése](./media/secure-web-app/ad-mfa-conditional-add.png)

A házirendet a képernyő felső részén található jelölőnégyzet bejelölésével vagy a **feltételes hozzáférés** lapon is engedélyezheti. Ha a házirend engedélyezve van, a felhasználóknak MFA-ra van szükségük a portálra való bejelentkezéshez.

Létezik egy alapkonfigurációra vonatkozó szabályzat, amely minden Azure-rendszergazda esetében MFA-t igényel. Azonnal engedélyezheti a portálon. Előfordulhat, hogy a házirend engedélyezése érvényteleníti az aktuális munkamenetet, és ismét be kell jelentkeznie.

Ha az alapkonfiguráció házirendje nincs engedélyezve:
1.  Válassza **az MFA megkövetelése rendszergazdák számára**lehetőséget.
2.  Válassza a **házirend azonnali használata**lehetőséget.

   ![Válassza a házirend azonnali használata lehetőséget](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Alkalmazások és erőforrások figyelése az Azure Sentinel használatával

Mivel az alkalmazás növekszik, nehéz lesz összesíteni az erőforrásoktól kapott összes biztonsági jelet és mérőszámot, és azok működés közbeni módon elérhetővé válnak.

Az Azure Sentinel úgy lett kialakítva, hogy adatokat gyűjtsön, észlelje a fenyegetések típusait, és betekintést nyújtson a biztonsági incidensekre.
Amíg a manuális beavatkozásra vár, az Azure Sentinel előre megírt forgatókönyvekkel is hivatkozhat a riasztások és az incidensek kezelési folyamatainak kiindítására.

A minta alkalmazás több, az Azure Sentinel által figyelhető erőforrásból áll.
Az Azure Sentinel beállításához először létre kell hoznia egy Log Analytics munkaterületet, amely a különböző erőforrásokból gyűjtött összes adatokat tárolja.

A munkaterület létrehozásához:

1. A Azure Portal keresési mezőjében keresse meg a **log Analytics**. Válassza **log Analytics**munkaterületek lehetőséget.

   ![Log Analytics munkaterületek keresése](./media/secure-web-app/sentinel-log-analytics.png)

    *Log Analytics munkaterületek keresése*

2. A következő lapon válassza a **Hozzáadás** lehetőséget, majd adjon meg egy nevet, egy erőforráscsoportot és egy helyet a munkaterületnek.
   ![Log Analytics-munkaterület létrehozása](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Log Analytics-munkaterület létrehozása*

3. Az **Azure Sentinel**kereséséhez használja a keresőmezőt.

   ![Az Azure Sentinel keresése](./media/secure-web-app/sentinel-add.png)

    *Az Azure Sentinel keresése*

4. Válassza a **Hozzáadás** lehetőséget, majd válassza ki a korábban létrehozott log Analytics munkaterületet.

   ![Log Analytics munkaterület hozzáadása](./media/secure-web-app/sentinel-workspace-add.png)

    *Log Analytics munkaterület hozzáadása*

5. Az **Azure Sentinel-** adatösszekötők lap **konfiguráció**területén válassza az adatösszekötők lehetőséget. Egy Azure-szolgáltatásokból álló tömb jelenik meg, amely a Log Analytics Storage-példányhoz kapcsolható az Azure Sentinel elemzéséhez.

   ![Log Analytics adatösszekötők](./media/secure-web-app/sentinel-connectors.png)

    *Adatösszekötő hozzáadása az Azure Sentinelhez*

   Például az Application Gateway összekapcsolásához hajtsa végre a következő lépéseket:

   1. Nyissa meg az Azure Application Gateway példány panelt.
   2. A **figyelés**válassza **diagnosztikai beállítások**.
   3. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget.

      ![Application Gateway diagnosztika hozzáadása](./media/secure-web-app/sentinel-gateway-connector.png)

      *Application Gateway diagnosztika hozzáadása*

   4. A **diagnosztikai beállítások** lapon válassza ki a létrehozott log Analytics munkaterületet, majd válassza ki az összes összegyűjteni kívánt metrikát, és küldje el az Azure sentinelnek. Kattintson a **Mentés** gombra.

        ![Azure Sentinel-összekötő beállításai](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure Sentinel-összekötő beállításai*

  Az erőforrás metrikái az Azure Sentinelben találhatók, ahol lekérdezheti és megvizsgálhatja azokat.

   Adja hozzá ugyanezeket a metrikákat a Azure Key Vault, a nyilvános IP-cím, a Azure Database for PostgreSQL és minden olyan szolgáltatás diagnosztikai beállításainál, amelyek támogatják a fiókjában a diagnosztikai naplókat.

A metrikák beállítása után az Azure Sentinel elemezni kívánt adatokat tartalmaz.

## <a name="evaluate-and-verify"></a>Értékelés és ellenőrzés
Az architektúra fejlesztése és üzembe helyezése után biztosítania kell, hogy a kód és a központilag telepített szolgáltatások megfeleljenek a biztonsági előírásoknak. A következő lépésekkel ellenőrizheti a szoftvereket:

- Statikus kód elemzése
- Sebezhetőségi vizsgálat
- Sebezhetőségek keresése és javítása az alkalmazás függőségeiben

Ezek a biztonságos fejlesztés ajánlott eljárásainak alapvető építőelemei.

### <a name="static-code-analysis"></a>Statikus kód elemzése
A minta alkalmazás esetében a statikus elemzési eszközökkel történő ellenőrzés az alkalmazás kódjában található biztonsági rések megkeresését jelenti, például a szennyező ellenőrzés és az adatáramlás-elemzés segítségével. A Python statikus elemzési eszközei nagyobb megbízhatóságot biztosítanak az alkalmazás biztonsága érdekében.

**Lint**

A PyFlakes egy Python-áttöltési könyvtár, amely lehetővé teszi a Dead Code és a nem használt függvények eltávolítását az alkalmazásokból az itt látható módon:

![PyFlakes](./media/secure-web-app/pyflakes.png)

A feltöltés olyan tippeket és lehetséges módosításokat tesz lehetővé, amelyek lehetővé teszik a kód megtisztítását, és a futás közben kevésbé valószínű hibákat.

**PyLint**

A PyLint a projekt legnagyobb értékét biztosította. A kód szabványos ellenőrzéseket, hibaellenőrzésokat és újrabontási tippeket hajt végre, így biztosítva, hogy a kiszolgálón futó kód biztonságban legyen. Ha a PyLint segítségével frissíti a kódot, a hibákat kihagyhatja, és javíthatja a PyLint-minősítést, ahogy az alábbi képek is megjelennek.

![PyLint előtt](./media/secure-web-app/before-pylint.png)

*PyLint előtt*

Miután kijavította a felmerülő eszközök által talált hibakódokat, nagyobb megbízhatósággal rendelkezik, hogy a kód nem a hibákra van kitéve. A hibák kijavítása jelentősen csökkenti azokat a biztonsági kockázatokat, amelyek akkor fordulhatnak elő, amikor a kód üzembe helyezése éles környezetekben történik.

![Pylint után](./media/secure-web-app/after-pylint.png)

*PyLint után*

### <a name="vulnerability-scanning"></a>Sebezhetőségi vizsgálat
A [OWASP Zap](https://www.zaproxy.org/) eszköze egy nyílt forráskódú, webalkalmazások sebezhetőségét vizsgáló képolvasó, amellyel ellenőrizhető, hogy milyen biztonsági réseket használhat a minta alkalmazás. Ha az eszközt a minta alkalmazásban futtatja, a rendszer néhány lehetséges hibát és támadási vektort feltár.

![ZAP-eszköz](./media/secure-web-app/zap-tool.png)

*ZAP-eszköz*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Biztonsági rések keresése és javítása az alkalmazás függőségeiben
Az alkalmazások függőségeinek megkereséséhez és kijavításához használhatja a [OWASP függőség-ellenőrzését](https://www.owasp.org/index.php/OWASP_Dependency_Check).

A biztonság egy hasonló alkalmazás, amely ellenőrzi a függőségeket. A [githubon](https://github.com/pyupio/safety)is megtalálhatja. A biztonsági rések a jól ismert sebezhetőségi adatbázisokban találhatók.

![Biztonsági](./media/secure-web-app/pysafety.png)

*Biztonsági*

## <a name="next-steps"></a>További lépések
A következő cikkek segíthetnek a biztonságos alkalmazások megtervezésében, fejlesztésében és üzembe helyezésében.

- [Tervezési](secure-design.md)
- [Fejlesztés](secure-develop.md)
- [Üzembe helyezés](secure-deploy.md)
