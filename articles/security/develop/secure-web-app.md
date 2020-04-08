---
title: Biztonságos webes alkalmazás kidolgozása | Microsoft dokumentumok
description: Ez az egyszerű mintaalkalmazás olyan biztonsági gyakorlati tanácsokat valósít meg, amelyek javítják az alkalmazást és a szervezet biztonsági állapotát az Azure-ban való fejlesztés során.
keywords: na
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
ms.openlocfilehash: 55c6d374c8a3c308323c0d003726492477e33ff8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811238"
---
# <a name="develop-a-secure-web-app"></a>Biztonságos webalkalmazások fejlesztése

Ez a minta egy egyszerű Python-alkalmazás, amely egy olyan weblapot jelenít meg, amely az Azure-beli alkalmazások fejlesztéséhez szükséges biztonsági erőforrásokra mutató hivatkozásokat jelenít meg. Az alkalmazás olyan biztonsági gyakorlati tanácsokat valósít meg, amelyek segíthetnek az alkalmazás és a szervezet biztonsági helyzetének javításában, amikor alkalmazásokat fejleszt az Azure-ban.

Az alkalmazás-összetevők megfelelő konfigurálásának biztosításához egymás után kövesse a cikkben ismertetett lépéseket. Az adatbázis, az Azure App Service, az Azure Key Vault-példány és az Azure Application Gateway-példány egymástól függ.

A központi telepítési parancsfájlok beállítják az infrastruktúrát. A központi telepítési parancsfájlok futtatása után az Azure Portalon manuálisan kell konfigurálnia az összetevők és szolgáltatások összekapcsolását.

A mintaalkalmazás az Azure-on olyan alkalmazásokat fejlesztő kezdők nek szól, akik biztonsági intézkedéseket szeretnének végrehajtani az alkalmazásaikban.

Az alkalmazás fejlesztése és üzembe helyezése során megtudhatja, hogyan:

- Hozzon létre egy Azure Key Vault-példányt, tároljon és szerezzen be titkokat belőle.
- Telepítse az Azure Database for PostgreSQL-t, állítson be biztonságos jelszavakat, és engedélyezze a hozzáférést.
- Futtasson egy Alpine Linux-tárolót az Azure Web Apps for Linux on, és engedélyezze az Azure-erőforrások felügyelt identitások.
- Hozzon létre és konfiguráljon egy Azure Application Gateway-példányt egy [OWASP Top 10 Ruleset](https://coreruleset.org/)rendszert használó tűzfallal.
- Engedélyezze az adatok titkosítását az átvitel és az inaktív szolgáltatások használatával.

Az alkalmazás fejlesztése és üzembe helyezése után a következő minta webalkalmazást, valamint a leírt konfigurációs és biztonsági intézkedéseket fogja beállítani.

![Minta webalkalmazás](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architektúra

Az alkalmazás egy tipikus n-szintű alkalmazás három réteggel. Az előtér, a háttérrendszer és az integrált figyelési és titkos felügyeleti összetevőket tartalmazó adatbázisréteg itt látható:

![Alkalmazásarchitektúra](./media/secure-web-app/architecture.png)

Az architektúra a következő összetevőkből áll:

- [Az Azure Application Gateway](../../application-gateway/index.yml)alkalmazást. Biztosítja az átjárót és a tűzfalat az alkalmazásarchitektúrához.
- [Azure Web Apps Linux on](../../app-service/containers/app-service-linux-intro.md). A tároló futásidejű a Python-alkalmazás Linux-környezetben való futtatásához.
- [Az Azure Key Vault](../../key-vault/index.yml). Tárolja és titkosítja az alkalmazás titkosítatait, és kezeli a hozzáférési szabályzatok létrehozását körülöttük.
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). Biztonságosan tárolja alkalmazásunk adatait.
- [Az Azure Security Center](../../security-center/index.yml) és [az Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Figyelést és riasztásokat biztosít az alkalmazás működéséről.

## <a name="threat-model"></a>Fenyegetésmodell

A fenyegetésmodellezés az üzleti és alkalmazásra leselkedő potenciális biztonsági fenyegetések azonosításának folyamata, majd annak biztosítása, hogy megfelelő kockázatcsökkentési terv legyen érvényben.

Ez a minta a [Microsoft Threat Modeling Tool segítségével](threat-modeling-tool.md) valósította meg a biztonságos mintaalkalmazás fenyegetésmodellezését. Az összetevők és az adatfolyamok diagrammal azonosíthatja a problémákat és fenyegetéseket a fejlesztési folyamat korai szakaszában. Ez időt és pénzt takarít meg később.

Ez a mintaalkalmazás fenyegetésmodellje:

![Fenyegetésmodell](./media/secure-web-app/threat-model.png)

Néhány minta fenyegetések és a potenciális biztonsági rések, amelyek a fenyegetés modellezési eszköz generálja a következő képernyőképen látható. A fenyegetésmodell áttekintést ad a támadási felület elérhetővé, és kéri a fejlesztőket, hogy gondolja át, hogyan lehet enyhíteni a problémákat.

![Fenyegetésmodell kimenete](./media/secure-web-app/threat-model-output.png)

Például az SQL-injektálás az előző fenyegetésmodell kimenetét a felhasználói bemenetek fertőtlenítése és a tárolt függvények használatával az Azure Database for PostgreSQL használatával. Ez a megoldás megakadályozza a lekérdezések tetszőleges végrehajtását az adatok olvasása és írása során.

A fejlesztők a fenyegetésmodell kimenetében lévő fenyegetések csökkentésével javítják a rendszer általános biztonságát.

## <a name="deployment"></a>Környezet

A következő lehetőségek lehetővé teszik a Linux futtatását az Azure App Service szolgáltatásban:

- Válasszon egy tárolót az Azure-beli, támogató technológiákkal (Python, Ruby, PHP, Java, Node.js, .NET Core) létrehozott, előre összeállított Microsoft-tárolók listájából.
- Használjon egyéni tárolót. Válassza ki a saját tároló-jegyzékek forrásaként a rendszerkép, és épít a számos rendelkezésre álló technológiák, amelyek támogatják a HTTP.Select your own container regissasas as the source of the image and build on the many available technologies that support HTTP.

Ebben a példában futtatja a központi telepítési parancsfájlt, amely telepíti a webapp-ra az App Service-re, és létrehozza az erőforrásokat.

Az alkalmazás használhatja a különböző telepítési modellek alább látható:

![Központi telepítési adatfolyam-diagram](./media/secure-web-app/deployment.png)

Az Azure-ban számos módon telepíthet alkalmazásokat, többek között:

- Azure Resource Manager-sablonok
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

Ez az alkalmazás használt:

- [Docker](https://docs.docker.com/) a tárolórendszerképek létrehozásához és létrehozásához.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) üzembe helyezéshez.
- [Docker Hub,](https://hub.docker.com/) mint a tároló beállításjegyzék.

## <a name="security-considerations"></a>Biztonsági szempontok

### <a name="network"></a>Network (Hálózat)

A mintaalkalmazás végpontok közötti TLS/SSL titkosítást használ a hálózatba beáramló és a hálózatból kiáramló, a tranzitadatokhoz. Az átjáró önaláírt tanúsítvánnyal van konfigurálva.
> [!IMPORTANT]
> Ebben az bemutatóban önaláírt tanúsítvány t használ. Éles környezetben a tanúsítványokat ellenőrzött hitelesítésszolgáltatótól (CA) kell beszereznie.

Az alkalmazás tűzfala is megvizsgálja a bejövő forgalmat, és figyelmezteti a rendszergazdákat, ha rosszindulatú forgalmat észlel a hálózati forgalomban.
Az Application Gateway csökkenti a fenyegetésmodellben felderített DDoS- és SQL-injektálási fenyegetések lehetőségét.

### <a name="identity"></a>Identitás

A portálra való bejelentkezéshez a mintaalkalmazás többtényezős hitelesítést használ az Azure Active Directory (Azure AD) rendszergazdáiszámára, akik az erőforrásokhoz kapnak hozzáférést.
A mintaalkalmazás felügyelt identitások használatával szerez engedélyeket az Azure Key Vault titkos kulcsainak olvasásához és lekéréséhez, biztosítva, hogy az alkalmazásnak nem kell titkos hitelesítő adatokat és jogkivonatokat kódolnia a titkos kulcsok olvasásához. Az Azure AD automatikusan létrehozza a szolgáltatásnéveket, amelyeket az alkalmazásnak el kell olvasnia, és módosítja a titkos kulcsokat a felügyelt identitások használatba kerülésekor.

Felügyelt identitások az Azure-erőforrások és az MFA megnehezíti az ellenfelek számára, hogy jogosultságot szerezzenek, és bővítsék a jogosultságaikat a rendszerben. Erre a fenyegetésre a fenyegetési modell is rámutatott.
Az alkalmazás oauth-ot használ, amely lehetővé teszi az OAuth alkalmazásban regisztrált felhasználók számára, hogy bejelentkezzenek az alkalmazásba.

### <a name="storage"></a>Storage

A PostgreSQL-adatbázisban lévő adatokat az Azure Database for PostgreSQL automatikusan titkosítja. Az adatbázis engedélyezi az App Service IP-címeit, így csak a telepített App Service-webalkalmazás férhet hozzá az adatbázis-erőforrásokhoz a megfelelő hitelesítési hitelesítő adatokkal.

### <a name="logging-and-auditing"></a>Naplózás

Az alkalmazás megvalósítja a naplózást az Application Insights használatával a metrikák, naplók és kivételek nyomon követésére. Ez a naplózás elegendő alkalmazásmetaadatot biztosít ahhoz, hogy tájékoztassa a fejlesztőket és a műveleti csapat tagjait az alkalmazás állapotáról. Biztonsági incidensek esetén elegendő adatot is biztosít a nyomon lépéshez.

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok

Ha még nem rendelkezik Azure-fiókkal, létrehozhat egy ingyeneset. Az [ingyenes fiókoldalra](https://azure.microsoft.com/free/) feladhatja a kezdéshez, megtudhatja, hogy mit tehet egy ingyenes Azure-fiókkal, és megtudhatja, hogy mely termékek 12 hónapig ingyenesek.

Az erőforrások üzembe helyezéséhez a mintaalkalmazásban a biztonsági funkciókkal, meg kell fizetnie néhány prémium funkciók. Mivel az alkalmazás méretei és az Azure által kínált ingyenes szintek és próbaverziók frissítése az alkalmazás követelményeinek teljesítése érdekében, a költségek növekedhetnek. Az Azure [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével megbecsülheti a költségeket.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

### <a name="prerequisites"></a>Előfeltételek

Az alkalmazás üzembe kapcsolásához telepítenie kell az alábbi eszközöket:

- Kódszerkesztő az alkalmazáskód módosításához és megtekintéséhez. [A Visual Studio Code](https://code.visualstudio.com/) egy nyílt forráskódú lehetőség.
- [Azure CLI](/cli/azure/install-azure-cli) a fejlesztői számítógépen.
- [Git](https://git-scm.com/) a rendszer. A Git a forráskód helyi klónozására szolgál.
- [jq](https://stedolan.github.io/jq/), egy UNIX eszköz a JSON felhasználóbarát lekérdezésére.

A mintaalkalmazás erőforrásainak üzembe helyezéséhez Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a mintaalkalmazás teszteléséhez.

Az eszközök telepítése után készen áll az alkalmazás azure-ra való üzembe helyezésére.

### <a name="environment-setup"></a>A környezet beállítása

Futtassa a központi telepítési parancsfájlokat a környezet és az előfizetés beállításához:

1. A forráskódtár klónozásához használja ezt a Git parancsot:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. A könyvtárba való beköltözéshez használja a következő parancsot:

   ```shell
   cd tutorial-project/scripts
   ```

3. Vannak olyan fájlok a parancsfájlok mappájában, amelyek a használt platformra (Windows vagy Linux) vonatkoznak. Mivel az Azure CLI már telepítve van, jelentkezzen be az Azure-fiókba a parancssorból az Azure CLI parancs futtatásával:

   ```azurecli-interactive
   az login
   ```

Megnyílik a böngésző, és bejelentkezik a hitelesítő adataival. A bejelentkezés után megkezdheti az erőforrások központi telepítését a parancssorból.

A központi `deploy-powershell.ps1` telepítési `deploy-bash.sh` parancsfájlok és a kódot tartalmazó, amely telepíti a teljes alkalmazást.
A megoldás üzembe helyezése:

1. Ha a PowerShell futtassa `deploy-powershell.ps1` a `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` fájlt a régió és az erőforráscsoport nevének megfelelő Azure-régiókra és az erőforráscsoport nevére való lecserélésével.
2. Ha Linuxon futtassa a `deploy-bash.sh` `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`fájlt a beírással, akkor lehet, hogy a fájlt végrehajthatóvá kell tennie a`chmod +x deploy-bash.sh`

Az alábbi példák a legfontosabb összetevők kódrészleteket mutatják be. A példákat egyenként vagy a többi összetevővel együtt is telepítheti a központi telepítés fájlok futtatásával.

### <a name="implementation-guidance"></a>Végrehajtási útmutató

A központi telepítési parancsfájl egy parancsfájl, amely négy fázisra bontható. Minden fázis telepíti és konfigurálja az [architektúradiagramban](#architecture)található Azure-erőforrást.

A négy fázis a következő:

- Telepítse az Azure Key Vaultot.
- Telepítse az Azure Database for PostgreSQL-t.
- Telepítse az Azure Web Apps linuxos telepítése.
- Telepítse az Application Gateway alkalmazást webalkalmazás-tűzfallal.

Minden fázis az előzőre épül a korábban üzembe helyezett erőforrások konfigurációjának használatával.

A megvalósítási lépések végrehajtásához győződjön meg arról, hogy telepítette az [Előfeltételek](#prerequisites)csoportban felsorolt eszközöket.

#### <a name="deploy-azure-key-vault"></a>Az Azure Key Vault telepítése

Ebben a szakaszban hozzon létre és telepítsen egy Azure Key Vault-példányt, amely titkos kulcsok és tanúsítványok tárolására szolgál.

Miután befejezte a központi telepítést, rendelkezik egy Azure Key Vault-példány üzembe helyezésével az Azure-ban.

Az Azure Key Vault üzembe helyezése az Azure CLI használatával:

1. Deklarálja az Azure Key Vault változóit.
2. Regisztrálja az Azure Key Vault-szolgáltatót.
3. Hozza létre a példány erőforráscsoportját.
4. Hozza létre az Azure Key Vault-példányt a 3.

   ```powershell-interactive

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

Ajánlott eljárás az Azure-erőforrások felügyelt identitások használata olyan alkalmazásokban, amelyek key vault használatával erőforrások eléréséhez. A biztonsági állapot növekszik, ha a Key Vault hozzáférési kulcsai nem kódvagy konfiguráció ban tárolódnak.

#### <a name="deploy-azure-database-for-postgresql"></a>Az Azure Database telepítése a PostgreSQL-hez

Az Azure Database for PostgreSQL a következőképpen működik, először hozza létre az adatbázis-kiszolgálót, majd hozza létre azt az adatbázist, amelyen a sémát és az adatokat tárolni szeretné.

A központi telepítés befejezése után egy PostgreSQL-kiszolgáló és az Azure-on futó adatbázis fut.

Az Azure Database for PostgreSQL üzembe helyezése az Azure CLI használatával:

1. Nyisson meg egy terminált az Azure CLI-vel és az Azure-előfizetés beállításával.
2. Hozzon létre egy biztonságos felhasználónevet és jelszókombinációt, amely az adatbázis eléréséhez használatos. (Ezeket az Azure Key Vaultban kell tárolni az őket használó alkalmazások számára.)
3. Hozza létre a PostgreSQL kiszolgálópéldányt.
4. Hozzon létre egy adatbázist a 3.
5. PostgreSQL parancsfájlok futtatása a PostgreSQL példányon.

Az alábbi kód az Azure KeyVault-ban tárolt PGUSERNAME és PGPASSWORD titkos kulcsokra támaszkodik a fenti keyvault-lépés központi telepítéséből.

   ```powershell-interactive
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

Az adatbázis üzembe helyezése után az Azure Key Vaultban kell tárolnia a hitelesítő adatait és a kapcsolati karakterláncot.
A parancsfájlok mappájában van `functions.sql` egy fájl, amely tartalmazza a PL / pgSQL kódot, amely tárolja a tárolt függvényeket, amikor futtatja. A fájl futtatása paraméterezi az SQL-injektálás korlátozásához a bemeneteket.

A PostgreSQL egy olyan `psql` eszközzel van eljárva, amely az adatbázishoz való csatlakozásra szolgál. A `functions.sql`futtatásához csatlakoznia kell az Azure Database for PostgreSQL-példányhoz a helyi számítógépről, és onnan kell futtatnia. Telepítése a psql eszköz tartalmazza az alapértelmezett telepítés PostgreSQL minden operációs rendszer.
További információt a [psql dokumentációban talál.](https://www.postgresql.org/docs/9.3/app-psql.html)

Az Azure Cloud `psql` Shell is tartalmazza az eszközt. A Cloud Shell közvetlenül az Azure Portalon keresztül is használható a Cloud Shell ikon kiválasztásával.

A PostgreSQL-példány távoli elérésének engedélyezéséhez engedélyeznie kell az IP-címet a PostgreSQL-ben.
Ezt a hozzáférést úgy engedélyezheti, hogy a **Kapcsolat biztonsága** lapra lép, az **Ügyfél IP hozzáadása**lehetőséget választja, és menti az új beállításokat.

![Ügyfél IP-címének engedélyezése](./media/secure-web-app/add-client-ip-postgres.png)

Ha a helyi psql eszköz helyett a Cloud Shellt használja, válassza az **Azure-szolgáltatások hoz való hozzáférés engedélyezése lehetőséget,** és módosítsa annak értékét **ON-ra** a Cloud Shell-hozzáférés engedélyezéséhez.

Ezután csatlakozzon a példányhoz az alábbi psql parancs futtatásával kapcsolati karakterlánc-paraméterekkel az Azure Portalon a PostgreSQL-példány **Kapcsolatkarakterláncok** lapján.
Cserélje le az üres kapcsos zárójelek az adatbázis Kapcsolati karakterlánc paneljének paramétereivel és a jelszóval az Azure Key Vault jelszavával.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Futtassa a következő PL/pgSQL parancsfájlt, miután meggyőződett arról, hogy csatlakozik az adatbázishoz. A parancsfájl létrehozza az adatok adatbázisba való beszúrásához használt tárolt függvényeket.

```shell
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

A TLS és a Hitelesítésszolgáltató (CA) PostgreSQL-ellenőrzés ének beállításáról a [TLS-kapcsolat konfigurálása az Azure Database for PostgreSQL alkalmazásban](/azure/postgresql/concepts-ssl-connection-security)című témakörben talál további információt.

A tárolóban egy főtanúsítvány is található. A tanúsítvány megszerzése érdekében tett lépések a következők:

1. Töltse le a tanúsítványfájlt a [hitelesítésszolgáltatótól.](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)
2. [Töltse le és telepítse az OpenSSL-t a gépére.](/azure/postgresql/concepts-ssl-connection-security)
3. A tanúsítványfájl dekódolása:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Tudjon meg többet arról, hogyan kell beállítani TLS biztonság PostgreSQL itt [konfigurálása TLS Connection Security](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Azure Web Apps telepítése Linuxon

Könnyedén hozhat létre Linux-szolgáltatásokat az Azure App Service-en felül, mivel az Azure előre összeállított tárolókat és lemezképeket biztosít a széles körben használt nyelvekhez, például a Pythonhoz, a Rubyhoz, a C#-hoz és a Java-hoz. Az Azure egyéni tárolókat is támogat, amelyek gyakorlatilag az összes programozási nyelv futtatását lehetővé teszik az Azure App Service platformon.

A telepített alkalmazás egy egyszerű Python alkalmazás, amely a legújabb Ubuntu Linux disztribúción fut. Az Azure Key Vault és a PostgreSQL-példányokhoz csatlakozik, amelyeket az előző szakaszokban hoztak létre a hitelesítő adatok és az adatok tárolása érdekében.

A következő Docker-fájl az alkalmazás gyökérmappájában található:

```dockerfile
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

A fenti Dockerfile az Azure Container Registry rendszerében tárolt `mcr.microsoft.com/samples/basic-linux-app`tároló létrehozásához használatos.

Az alábbi kód:

1. Deklarálja az App Service-példány változóit és neveit.
2. Létrehozza az Erőforráscsoportot az App Service-csomaghoz.
3. Az Azure Web Apps linuxos tárolók példányának leszolgáltatása.
4. Engedélyezi a webalkalmazás-tároló naplózását.
5. Beállít néhány alkalmazáskonfigurációt a tároló alkalmazásbeállításaiban.

   ```powershell-interactive
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

Ez a parancsfájl létrehoz egy hozzárendelt identitást az App Service-példány, amely az MSI-vel használható az Azure Key Vault használatával, kód vagy konfiguráció ban titkos kulcsok szigorú kódolása nélkül.

Nyissa meg az Azure Key Vault-példányt a portálon, hogy engedélyezze a hozzárendelt identitást a hozzáférési szabályzat **lapon.** Az **Egyszerű kiválasztása csoportban**keresse meg a létrehozott App Service-példány nevéhez hasonló alkalmazásnevet.
Az alkalmazáshoz csatolt egyszerű szolgáltatásnak láthatónak kell lennie. Jelölje ki, és mentse a hozzáférési szabályzat lapját, ahogy az az alábbi képernyőképen látható.

Mivel az alkalmazásnak csak kulcsokat kell letöltenie, válassza az Engedély **beolvasása** a titkos kulcsok beállításokat, amely engedélyezi a hozzáférést, miközben csökkenti a megadott jogosultságokat.

![A Key Vault hozzáférési irányelvei](./media/secure-web-app/kv-access-policy.png)

*Key Vault-hozzáférési házirend létrehozása*

Mentse a hozzáférési házirendet, majd mentse az új módosítást a **Hozzáférési házirendek** lapon a házirendek frissítéséhez.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Alkalmazásátjáró központi telepítése engedélyezve a webalkalmazás tűzfalával

A webes alkalmazásokban nem ajánlott a szolgáltatásokat közvetlenül a külvilág számára elérhetővé tenni az interneten.
A terheléselosztás és a tűzfalszabályok nagyobb biztonságot nyújtanak, és szabályozhatják a bejövő forgalmat, és segítenek annak kezelésében.

Alkalmazásátjáró-példány telepítése:

1. Hozza létre az erőforráscsoportot az alkalmazásátjáró elhelyezésére.
2. Az átjáróhoz csatolandó virtuális hálózat kiépítése.
3. Hozzon létre egy alhálózatot az átjáróhoz a virtuális hálózatban.
4. Nyilvános IP-cím kiépítése.
5. Az alkalmazásátjáró kiépítése.
6. Engedélyezze a webalkalmazás tűzfalát az átjárón.

   ```powershell-interactive
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

1. Új önaláírt tanúsítványt hoz létre az Azure-ban.
2. Az önaláírt tanúsítványt base64 kódolású fájlként tölti le.
3. Létrehoz egy jelszót az önaláírt tanúsítványhoz.
4. A tanúsítványt a jelszóval aláírt PFX-fájlként exportálja.
5. A tanúsítvány jelszavát az Azure Key Vaultban tárolja.

Ez a szakasz telepíti az alkalmazásátjárót:

```powershell-interactive
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

A központi telepítés befejezése után egy alkalmazásátjáró, amelyen engedélyezve van a webalkalmazás tűzfala.

Az átjárópéldány https-hez való 443-as portot teszi elérhetővé. Ez a konfiguráció biztosítja, hogy az alkalmazás unk csak a 443-as https-porton érhető el.

A nem használt portok blokkolása és a támadási felület expozíciójának korlátozása biztonsági gyakorlat.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Hálózati biztonsági csoportok hozzáadása az App Service-példányhoz

Az App Service-példányok integrálhatók a virtuális hálózatokkal. Ez az integráció lehetővé teszi, hogy konfigurálható a hálózati biztonsági csoport házirendek, amelyek kezelik az alkalmazás bejövő és kimenő forgalmat.

1. A funkció engedélyezéséhez az Azure App szolgáltatáspéldány **panelen**a Beállítások csoportban válassza a **Hálózat lehetőséget.** A jobb oldali ablaktáblában a **Virtuálishálózat-integráció**csoportban válassza a **Kattintson ide lehetőséget a konfigurálásához.**

   ![Új virtuális hálózati integráció](./media/secure-web-app/app-vnet-menu.png)

    *Új virtuális hálózati integráció az App Service-hez*

1. A következő lapon válassza a **VNET hozzáadása (előnézet) lehetőséget.**

1. A következő menüben válassza ki a telepítésben `hello-vnet`létrehozott virtuális hálózatot, amely a alkalmazáslal kezdődik. Létrehozhat egy új alhálózatot, vagy kijelölhet egy meglévőt.
   Ebben az esetben hozzon létre egy új alhálózatot. Állítsa a **Cím tartományt** **10.0.3.0/24-re,** és nevezze el az alhálózati **alkalmazás-alhálózatot.**

   ![Az App Service virtuális hálózati konfigurációja](./media/secure-web-app/app-vnet-config.png)

    *Virtuális hálózati konfiguráció az App Service-hez*

Most, hogy engedélyezte a virtuális hálózati integrációt, hálózati biztonsági csoportokat adhat az alkalmazáshoz.

1. Használja a keresőmezőt, keressen **hálózati biztonsági csoportokat**. Az eredmények között válassza a **Hálózati biztonsági csoportok** lehetőséget.

    ![Hálózati biztonsági csoportok keresése](./media/secure-web-app/nsg-search-menu.png)

    *Hálózati biztonsági csoportok keresése*

2. A következő menüben válassza a **Hozzáadás parancsot.** Adja meg annak az NSG-nek és **erőforráscsoportnak** a **nevét,** amelyben lennie kell. Ez az NSG az alkalmazásátjáró alhálózatára lesz alkalmazva.

    ![NSG létrehozása](./media/secure-web-app/nsg-create-new.png)

    *NSG létrehozása*

3. Az NSG létrehozása után válassza ki. A panelen a **Beállítások**csoportban válassza **a Bejövő biztonsági szabályok lehetőséget.** Konfigurálja ezeket a beállításokat úgy, hogy a 443-as porton keresztül az alkalmazásátjáróba érkező kapcsolatok is elérhetők.

   ![Az NSG konfigurálása](./media/secure-web-app/nsg-gateway-config.png)

   *Az NSG konfigurálása*

4. Az átjáró NSG kimenő szabályaiban adjon hozzá egy szabályt, amely lehetővé teszi a kimenő kapcsolatokat az App Service-példányhoz a szolgáltatáscímkét `AppService`megcélozó szabály létrehozásával:

   ![Kimenő szabályok hozzáadása az NSG-hez](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Kimenő szabályok hozzáadása az NSG-hez*

    Adjon hozzá egy másik kimenő szabályt, amely lehetővé teszi, hogy az átjáró kimenő szabályokat küldjön egy virtuális hálózatra.

   ![Másik kimenő szabály hozzáadása](./media/secure-web-app/nsg-outbound-vnet.png)

    *Másik kimenő szabály hozzáadása*

5. Az NSG alhálózati paneljén válassza a **Társítás**lehetőséget, válassza ki a központi telepítésben létrehozott virtuális hálózatot, és válassza ki a **gw-alhálózat nevű átjáró-alhálózatot.** Az NSG az alhálózatra van alkalmazva.

6. Hozzon létre egy másik NSG, mint a korábbi lépésben, ezúttal az App Service-példány. Mondj neki egy nevet. Adja hozzá a 443-as port bejövő szabályát, ahogy azt az nsg-hez az alkalmazásátjáróhoz.

   Ha egy App Service-példány telepítve van egy App Service-környezet-példány, amely nem ez a helyzet az alkalmazás, hozzáadhat bejövő szabályokat, amelyek lehetővé teszik az Azure Service Health-mintavételek megnyitásával portok 454-455 az App Service NSG bejövő biztonsági csoportok. Itt a konfiguráció:

   ![Szabályok hozzáadása az Azure Service Health-mintavételekhez](./media/secure-web-app/nsg-create-healthprobes.png)

    *Az Azure Service Health-mintavételek szabályainak hozzáadása (csak Az App Service-környezetben)*

7. A kimenő biztonsági szabályokban hozzon létre egy új kimenő biztonsági szabályt, amely lehetővé teszi, hogy az App Service-példány kommunikáljon a PostgreSQL-adatbázissal. Konfigurálja a következőket:

   ![A kimenő PostgreSQL-kapcsolatok engedélyezésére adott szabály](./media/secure-web-app/nsg-outbound-postgresql.png)

   *A kimenő PostgreSQL-kapcsolatok engedélyezéséhez tartozó szabály hozzáadása*

A támadási felület korlátozásához módosítsa az App Service hálózati beállításait úgy, hogy csak az alkalmazásátjáró férhessen hozzá az alkalmazáshoz.
Ehhez lépjen be az App Service-hálózat fülre, válassza az **IP-korlátozások** lapot, és hozzon létre egy engedélyezési szabályt, amely lehetővé teszi, hogy csak az alkalmazásátjáró IP-címe férhessen hozzá közvetlenül a szolgáltatáshoz.

Az átjáró IP-címét az áttekintő lapról kérheti le. Az **IP-cím CIDR** lapján adja meg `<GATEWAY_IP_ADDRESS>/32`az IP-címet a következő formátumban: .

![Csak az átjáró engedélyezése](./media/secure-web-app/app-allow-gw-only.png)

*Csak az átjáró IP-címének engedélyezése az App Service eléréséhez*

#### <a name="implement-azure-active-directory-oauth"></a>Az Azure Active Directory oauth megvalósítása

A minta webalkalmazás-lapon terjesztett Azure-dokumentumok olyan erőforrások az alkalmazásunkban, amelyek védelemre szorulnak. Az Azure Active Directory (Azure AD) segítségével különböző hitelesítési folyamatok használatával valósíthatja meg a webes, asztali és mobilalkalmazások hitelesítését.
Az alkalmazás **a Bejelentkezés a Microsofttal**, amely lehetővé teszi az alkalmazás számára, hogy olvassa el az egybérlős Azure AD-felhasználó listájához hozzáadott felhasználók profiljait.

Az Azure Portalon konfigurálja az alkalmazást a szükséges hitelesítő adatok használatára:

1. Válassza az **Azure Active Directory**t, vagy keresse meg a keresőmező használatával.

2. Válassza **az Új regisztráció lehetőséget:**

   ![Regisztráció létrehozása](./media/secure-web-app/ad-auth-create.png)

   *Azure AD-alkalmazás regisztrációjának létrehozása*

3. A következő lapon adja meg az alkalmazás nevét. A **Támogatott fióktípusok csoportban**válassza **a Csak ebben a szervezeti címtárban a Fiókok**lehetőséget.
    Az **Átirányítás URI**csoportban adja meg azt az alaptartományt, amelyen az alkalmazás futni fog, plusz egy a jogkivonat-végpontdal. Például: *GATEWAY_HASH*.cloudapp.net/token.

   ![Az Azure AD-alkalmazás regisztrációjának konfigurálása](./media/secure-web-app/ad-auth-type.png)

   *Az Azure AD-alkalmazás regisztrációjának konfigurálása*

4. Megjelenik egy képernyő, amely a regisztrált alkalmazást és annak adatait jeleníti meg. Ezt az információt hozzá kell adnia az Azure Key Vault-példányhoz.
   1. Másolja az alkalmazás (ügyfél) azonosítóját, és `CLIENTID`mentse a Key Vaultban .
   2. Másolja az előző lépésben megadott átirányítási URI-t, és mentse a ( más ) néven. `REDIRECTURI`
   3. Másolja az Azure AD alapértelmezett könyvtárnevét, amelynek *formátumneve*.microsoftonline.com, `TENANT`és mentse a Key Vault-ba.
   4. Nyissa meg a **tanúsítványok & titkos kulcsok** lapot az Azure AD alkalmazás, amely korábban létrehozott, és válassza az Új ügyfél **titkos**kulcsot , ahogy az alábbi képernyőképen látható. Állítson be egy lejárati dátumot, majd másolja `CLIENTSECRET`a létrehozott értéket, és mentse a Key Vaultban .

      ![Az Azure AD engedélyezési titkos](./media/secure-web-app/ad-auth-secrets.png)

      *Az Azure AD engedélyezési titkos*

   5. Hozzon létre egy biztonságos véletlen titkos kulcsot bármely parancssori/ online eszközzel. Mentse a Key `FLASKSECRETKEY`Vault ba. Az alkalmazáskeretrendszer ezt a kulcsot használja munkamenetek létrehozásához.
        A titkos kulcs létrehozásáról a [Lombik-munkamenetek ( Lombik-munkamenetek ) (Lombik-munkamenetek) (Lombik-munkamenetek) (Lombik-munkamenetek) (Lombik-munkamenet](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)

5. A bejelentkezés konfigurálása után hozzá kell adnia a felhasználókat az Azure AD-hivatkozáshoz, hogy azok bejelentkezhessenek az erőforrásba. Hozzáadásukhoz nyissa meg az Azure AD **Felhasználók** lapját, válassza a **Minden felhasználó**lehetőséget, majd válassza az **Új felhasználó** vagy az **Új vendégfelhasználó lehetőséget.** A teszteléshez hozzáadhat egy vendégfelhasználót, és meghívhatja a felhasználót a címtárba. Vagy hozzáadhat egy új felhasználót, ha az a tartomány, amelyen az alkalmazás fut, érvényesítve van. Ebben a példában csak az Azure AD-bérlőben regisztrált felhasználók regisztrálhatók a hozzáféréshez. A több-bérlős bejelentkezési hozzáférésről a dokumentációban olvashat.

   ![Felhasználók hozzáadása az alapértelmezett tartományhoz](./media/secure-web-app/ad-auth-add-user.png)

   *Felhasználók hozzáadása az alapértelmezett Azure Active Directory-tartományhoz*

Miután hozzáadja az Azure AD-konfigurációt és titkos kulcsokat a Key Vaulthoz, a felhasználók az Azure OAuth-hitelesítés használatával hitelesíthetők az alkalmazásban.
Az alkalmazáskódban ezt az Azure Active Directory hitelesítési könyvtár (ADAL) kezeli.

Miután a titkos kulcsok a Key Vaultban vannak, és az alkalmazás hozzáfér a titkos kulcsokhozhttps://GATEWAY_HASH.cloudapp.net)és az adatbázishoz, az alkalmazásszolgáltatás elérhető az átjáró alkalmazás URL-címén ( , amely a panelből szerezhető be.

Ha az Azure AD-be való bejelentkezéskor egy hibaüzenet jelenik meg, amely szerint "A felhasználó nincs regisztrálva abban a címtárban, amelybe be szeretne jelentkezni", hozzá kell adnia a felhasználót. A felhasználó hozzáadásához lépjen az Azure AD **Felhasználók** lapjára, és adja hozzá manuálisan a felhasználót az adatuk megadásával, vagy hívja meg a felhasználót úgy, hogy megadja az e-mail címét vendégfelhasználóként az Azure AD-be a **Vendég meghívása** panelen.

#### <a name="deploy-application-insights"></a>Az Application Insights üzembe helyezése
Most, hogy az alkalmazás üzembe helyezése és működése, az alkalmazáson belül előforduló hibákat, valamint a naplózási és nyomkövetési adatgyűjtést kell kezelnie.
A naplózási és nyomkövetési adatgyűjtés az alkalmazásban bekövetkező naplózási eseményeket tekinti meg.

Az Application Insights egy olyan szolgáltatás, amely a felhasználók vagy a rendszer által létrehozható naplókat gyűjti.

Application Insights-példány létrehozása:

1. Az **Application Insights** keresése az Azure Portal keresőmezőjének használatával.
2. Válassza az **Application Insights** lehetőséget. Adja meg az itt látható részleteket egy példány létrehozásához.

   ![Application Insights-példány létrehozása](./media/secure-web-app/app-insights-data.png)

A központi telepítés befejezése után egy Application Insights-példányt.

Az Applications Insights-példány létrehozása után meg kell, hogy az alkalmazás tudatában legyen a műszerezési kulcs, amely lehetővé teszi, hogy naplókat küld jön a felhőbe. Ehhez az Application Insights-kulcs lekérésével és az Azure által az Application Insights hoz biztosított alkalmazáskódtárban való használatával. Az ajánlott eljárás a kulcsok és a titkos kulcsok tárolása az Azure Key Vaultban a biztonságuk érdekében.

Az alapszintű mintaalkalmazás hozlétre az Applications Insights-példány létrehozása után, meg kell, hogy az alkalmazás tudatában a műszerezési kulcs, amely lehetővé teszi, hogy naplókat küldeni a felhőbe.
A Key Vaultban `APPINSIGHTSKEY` állítson be egy titkos kulcsot, és állítsa be annak értékét műszerezési kulcsként. Ez lehetővé teszi, hogy az alkalmazás naplókat és mutatókat küldjön az Application Insightsnak.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Többtényezős hitelesítés megvalósítása az Azure Active Directoryhoz

A rendszergazdáknak gondoskodniuk kell arról, hogy a portálon lévő előfizetési fiókok védettek legyenek. Az előfizetés ki van téve a támadásoknak, mert kezeli a létrehozott erőforrásokat. Az előfizetés védelme érdekében engedélyezze a többtényezős hitelesítést az **előfizetés Azure Active Directory** lapján.

Az Azure AD olyan szabályzatok alapján működik, amelyek egy bizonyos feltételeknek megfelelő felhasználóra vagy felhasználói csoportokra vonatkoznak.
Az Azure létrehoz egy alapértelmezett szabályzatot, amely meghatározza, hogy a rendszergazdáknak kétfaktoros hitelesítésre van szükségük a portálra való bejelentkezéshez.
A szabályzat engedélyezése után előfordulhat, hogy a rendszer kéri, hogy jelentkezzen ki, és jelentkezzen be újra az Azure Portalra.

Az MFA engedélyezése a rendszergazdai bejelentkezések számára:

1. Nyissa meg az **Azure Active Directory** lapot az Azure Portalon
2. A biztonsági kategória alatt válassza a feltételes hozzáférést. Ekkor az alábbi képernyő jelenik meg:

   ![Feltételes hozzáférés – házirendek](./media/secure-web-app/ad-mfa-conditional-add.png)

Ha nem tud új házirendet létrehozni:

1. Nyissa meg az **MFA** lapot.
2. Válassza ki az Azure AD Premium **ingyenes próbahivatkozást** az ingyenes próbaverzióra való előfizetéshez.

   ![Az Azure AD Premium ingyenes próbaverziója](./media/secure-web-app/ad-trial-premium.png)

Visszatérés a feltételes hozzáférési képernyőre.

1. Válassza az új házirend lapot.
2. Adja meg a házirend nevét.
3. Jelölje ki azokat a felhasználókat vagy csoportokat, amelyeknek engedélyezni szeretné az MFA-t.
4. Az **Access-vezérlők csoportban**jelölje be a **Grant** fül, majd a **Többtényezős hitelesítés megkövetelése** (és egyéb beállítások szükség esetén) lehetőséget.

   ![MFA megkövetelése](./media/secure-web-app/ad-mfa-conditional-add.png)

A házirendet a képernyő tetején lévő jelölőnégyzet bejelölésével engedélyezheti, vagy a **Feltételes hozzáférés** lapon. Ha a házirend engedélyezve van, a felhasználóknak többfunkciós kell lennia a portálra való bejelentkezéshez.

Van egy alaptervi szabályzat, amely az összes Azure-rendszergazdák többszintű szolgáltatását igényli. Engedélyezheti azt azonnal a portálon. A házirend engedélyezése érvénytelenítheti az aktuális munkamenetet, és kényszerítheti, hogy újra jelentkezzen be.

Ha az alapházirend nincs engedélyezve:

1. Válassza **az MFA megkövetelése a rendszergazdákszámára**lehetőséget.
2. Válassza a **Házirend használata azonnal**lehetőséget.

   ![Azonnal válassza a Házirend használata lehetőséget.](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Alkalmazások és erőforrások figyelése az Azure Sentinel segítségével

Az alkalmazás növekedésével nehéz lesz összesíteni az erőforrásokból kapott összes biztonsági jelet és metrikát, és azokat műveletorientált módon hasznossá tenni.

Az Azure Sentinel célja az adatok gyűjtése, a lehetséges fenyegetések típusának észlelése és a biztonsági incidensek láthatósága.
Amíg manuális beavatkozásra vár, az Azure Sentinel előre megírt forgatókönyvekre támaszkodhat a riasztások és az incidenskezelési folyamatok indításához.

A mintaalkalmazás több erőforrásból áll, amelyeket az Azure Sentinel figyelhet.
Az Azure Sentinel beállításához először létre kell hoznia egy Log Analytics-munkaterületet, amely tárolja a különböző erőforrásokból gyűjtött összes adatot.

A munkaterület létrehozása:

1. Az Azure Portal keresőmezőjében keresse meg a **Log Analytics**kifejezést. Válassza **a Log Analytics-munkaterületek lehetőséget.**

   ![Log Analytics-munkaterületek keresése](./media/secure-web-app/sentinel-log-analytics.png)

    *Log Analytics-munkaterületek keresése*

2. A következő lapon válassza a **Hozzáadás** lehetőséget, majd adja meg a munkaterület nevét, erőforráscsoportját és helyét.
   ![A Log Analytics-munkaterület létrehozása](./media/secure-web-app/sentinel-log-analytics-create.png)

   *A Log Analytics-munkaterület létrehozása*

3. A keresőmező segítségével keresse meg az **Azure Sentinel**.

   ![Az Azure Sentinel keresése](./media/secure-web-app/sentinel-add.png)

    *Az Azure Sentinel keresése*

4. Válassza **a Hozzáadás** lehetőséget, majd válassza ki a korábban létrehozott Log Analytics-munkaterületet.

   ![Log Analytics-munkaterület hozzáadása](./media/secure-web-app/sentinel-workspace-add.png)

    *Log Analytics-munkaterület hozzáadása*

5. Az **Azure Sentinel – Adatösszekötők** lap **Konfiguráció csoportban**válassza az **Adatösszekötők**lehetőséget. Megjelenik egy azure-szolgáltatások tömbje, amely az Azure Sentinel ben elemzésre hivatkozhat a Log Analytics-tárházhoz.

   ![Log Analytics-adatösszekötők](./media/secure-web-app/sentinel-connectors.png)

    *Adatösszekötő hozzáadása az Azure Sentinelhez*

   Az alkalmazásátjáró csatlakoztatásához például tegye a következőket:

   1. Nyissa meg az Azure Application Gateway példány panel.
   2. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.
   3. Válassza **a Diagnosztikai beállítás hozzáadása lehetőséget.**

      ![Alkalmazásátjáró diagnosztikájának hozzáadása](./media/secure-web-app/sentinel-gateway-connector.png)

      *Alkalmazásátjáró diagnosztikájának hozzáadása*

   4. A **Diagnosztikai beállítások** lapon válassza ki a létrehozott Log Analytics-munkaterületet, majd válassza ki az azure Sentinelnek gyűjtésre és elküldésre kívánt összes metrikát. Kattintson a **Mentés** gombra.

        ![Az Azure Sentinel-összekötő beállításai](./media/secure-web-app/sentinel-connector-settings.png)

        *Az Azure Sentinel-összekötő beállításai*

  Az erőforrás metrikák az Azure Sentinel, ahol lekérdezheti és kivizsgálhatja őket.

   Adja hozzá ugyanazokat a metrikákat az Azure Key Vault diagnosztikai beállításaiban, a nyilvános IP-címben, az Azure Database for PostgreSQL-ben és a fiókjában diagnosztikai naplókat támogató szolgáltatásokban.

A metrikák beállítása után az Azure Sentinel elemzési adatokat.

## <a name="evaluate-and-verify"></a>Értékelés és ellenőrzés

Az architektúra fejlesztése és üzembe helyezése után meg kell győződnie arról, hogy a kód és az üzembe helyezett szolgáltatások megfelelnek a biztonsági szabványoknak. A következő lépéseket teheti meg a szoftver ellenőrzéséhez:

- Statikus kód elemzése
- Biztonsági rés vizsgálata
- Az alkalmazásfüggőségek biztonsági réseinek keresése és javítása

Ezek a biztonságos fejlesztés bevált gyakorlatai alapvető építőkövei.

### <a name="static-code-analysis"></a>Statikus kód elemzése

A mintaalkalmazás esetében a statikus elemzési eszközökkel végzett ellenőrzés magában foglalja az alkalmazáskód biztonsági rései nek keresését olyan technikák alkalmazásokkal, mint a beaint-ellenőrzés és az adatáramlás-elemzés. A Python statikus elemző eszközei nagyobb megbízhatóságot biztosítanak az alkalmazás biztonságának biztosításában.

**Szösz**

PyFlakes, a Python szöszszín könyvtár, segít eltávolítani halott kódot és a nem használt funkciókat alkalmazások, ahogy itt látható:

![PyFlakes (PyFlakes)](./media/secure-web-app/pyflakes.png)

A szöszles idoben tippeket és lehetséges módosításokat biztosít, amelyek a kód ot tisztábbá és kevésbé hibahasznatokozhatják futásidőben.

**PyLint között**

A PyLint adta a projekt legnagyobb értékét. Kódszabvány-ellenőrzéseket, hibaellenőrzést és refactoring tippeket hajt végre annak érdekében, hogy a kiszolgálón futó kód biztonságos legyen. A PyLint használatával frissítheti a kódot, kiküszöbölheti a hibákat, és javíthatja a PyLint minősítést, ahogy az alábbi képek mutatják.

![Mielőtt PyLint](./media/secure-web-app/before-pylint.png)

*Mielőtt PyLint*

Miután kijavította a szöszlesi eszközök által talált kódhibákat, nagyobb biztos lehet abban, hogy a kód nem hajlamos a hibákra. A hibák kijavítása jelentősen csökkenti azokat a biztonsági kockázatokat, amelyek akkor jelentkezhetnek, ha a kód éles környezetben van telepítve.

![Pylint után](./media/secure-web-app/after-pylint.png)

*Miután PyLint*

### <a name="vulnerability-scanning"></a>Biztonsági rés vizsgálata

[Az OWASP ZAP eszköze](https://www.zaproxy.org/) egy nyílt forráskódú webalkalmazás-biztonsági résellenőrző, amellyel ellenőrizheti a mintaalkalmazás biztonsági réseit. Az eszköz futtatása a mintaalkalmazásban néhány lehetséges hibát és támadási vektort tár fel.

![ZAP eszköz](./media/secure-web-app/zap-tool.png)

*ZAP eszköz*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Az alkalmazásfüggőségek biztonsági rései nek keresése és javítása

Az alkalmazásfüggőségek megkereséséhez és javításához [használhatja az OWASP függőségi ellenőrzése](https://www.owasp.org/index.php/OWASP_Dependency_Check).

A biztonság egy hasonló alkalmazás, amely ellenőrzi a függőségeket. Megtalálható a [GitHubon.](https://github.com/pyupio/safety) Biztonsági vizsgálatok a jól ismert biztonsági résadatbázisokban található biztonsági rések után.

![Biztonsági](./media/secure-web-app/pysafety.png)

*Biztonsági*

## <a name="next-steps"></a>További lépések

Az alábbi cikkek segítségével biztonságos alkalmazásokat tervezhet, fejleszthet és helyezhet üzembe.

- [Tervezés](secure-design.md)
- [Fejlesztés](secure-develop.md)
- [Telepítés](secure-deploy.md)
