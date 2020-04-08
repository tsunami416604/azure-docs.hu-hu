---
title: Alkalmazások konfigurálása a portálon
description: Ismerje meg, hogyan konfigurálhatja az Alkalmazásszolgáltatás-alkalmazások általános beállításait az Azure Portalon. Alkalmazásbeállítások, kapcsolati karakterláncok, platform, nyelvi verem, tároló stb.
keywords: Azure app service, web app, alkalmazásbeállítások, környezeti változók
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: seodec18
ms.openlocfilehash: 18469c94b66acab27b58243e8d15eb924843319b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811115"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>App Service-alkalmazás konfigurálása az Azure Portalon

Ez a témakör bemutatja, hogyan konfigurálhatja a webes alkalmazások, a mobil háttér- és api-alkalmazások általános beállításait az [Azure Portal]használatával.

## <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása

Az App Service-ben az alkalmazásbeállítások olyan változók, amelyek környezeti változókként kerülnek átadásra az alkalmazáskódnak. Linux-alkalmazások és egyéni tárolók esetén az App Service `--env` továbbítja az alkalmazásbeállításokat a tárolóba a jelző használatával a környezeti változó beállítása a tárolóban.

Az [Azure Portalon]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást. 

![Alkalmazásszolgáltatások keresése](./media/configure-common/search-for-app-services.png)

Az alkalmazás bal oldali menüjében válassza a **Konfigurációs** > **alkalmazás beállításai**lehetőséget.

![Alkalmazásbeállítások](./media/configure-common/open-ui.png)

A ASP.NET és ASP.NET Core-fejlesztők számára az alkalmazásbeállítások `<appSettings>` beállítása az App Service szolgáltatásban olyan, mint a *Web.config* vagy az *appsettings.json*fájlbeállítása, de az App Service értékei felülbírálják a *Web.config* vagy *az appsettings.json fájlértékeit.* A fejlesztői beállításokat (például helyi MySQL-jelszót) a *Web.config* vagy *az appsettings.json*fájlban tarthatja meg, de az éles titkok (például az Azure MySQL adatbázis-jelszó) biztonságosak az App Service-ben. Ugyanez a kód a fejlesztési beállításokat használja, ha helyihiba, és használja az éles titok, amikor az Azure-ba üzembe helyezett.

Más nyelvi halmok, hasonlóképpen, az alkalmazás beállításait, mint a környezeti változók futásidőben. A nyelv-verem specifikus lépéseket lásd:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Egyéni tárolók](containers/configure-custom-container.md#configure-environment-variables)

Az alkalmazásbeállítások tárolásakor mindig titkosítva vannak (inketttitkosítás).

> [!NOTE]
> Az alkalmazásbeállítások a [Key Vault-hivatkozások](/azure/key-vault/) használatával is feloldhatók a Key [Vaultból.](app-service-key-vault-references.md)

### <a name="show-hidden-values"></a>Rejtett értékek megjelenítése

Alapértelmezés szerint az alkalmazásbeállítások értékei biztonsági okokból rejtve maradnak a portálon. Az alkalmazásbeállítás rejtett értékének megtekintéséhez kattintson a beállítás **Érték** mezőjére. Az összes alkalmazásbeállítás értékeinek megtekintéséhez kattintson az **Érték megjelenítése** gombra.

### <a name="add-or-edit"></a>Hozzáadás vagy szerkesztés

Új alkalmazásbeállítás hozzáadásához kattintson az **Új alkalmazásbeállítás gombra.** A párbeszédpanelen [a beállítást az aktuális foglalatra ragaszthatja.](deploy-staging-slots.md#which-settings-are-swapped)

A beállítások szerkesztéséhez kattintson a jobb oldalon található **Szerkesztés** gombra.

Ha végzett, kattintson a **Frissítés gombra.** Ne felejtsen el a **Mentés** gombra kattintani a **Konfiguráció** lapon.

> [!NOTE]
> Egy alapértelmezett Linux-tárolóban vagy egy egyéni Linux-tárolóban az alkalmazásbeállítás `ApplicationInsights:InstrumentationKey` nevének bármely beágyazott JSON-kulcsstruktúráját az App Service-ben kell konfigurálni a `ApplicationInsights__InstrumentationKey` kulcsnévhez. Más szóval, `:` minden kell `__` cserélni (dupla aláhúzás).
>

### <a name="edit-in-bulk"></a>Szerkesztés ömlesztve

Ha tömegesen szeretné hozzáadni vagy szerkeszteni az alkalmazásbeállításokat, kattintson a **Speciális szerkesztés** gombra. Ha végzett, kattintson a **Frissítés gombra.** Ne felejtsen el a **Mentés** gombra kattintani a **Konfiguráció** lapon.

Az alkalmazásbeállítások a következő JSON-formázással rendelkeznek:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>Kapcsolati sztringek konfigurálása

Az [Azure Portalon]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **Konfigurációs** > **alkalmazás beállításai**lehetőséget.

![Alkalmazásbeállítások](./media/configure-common/open-ui.png)

A ASP.NET és ASP.NET Core fejlesztők számára a kapcsolati `<connectionStrings>` karakterláncok beállítása az App Service szolgáltatásban olyan, mintha a *Web.config*fájlban állítanák be őket, de az App Service-ben beállított értékek felülírják a *Web.config*fájlban lévőket. A fejlesztői beállításokat (például egy adatbázisfájlt) a *Web.config* fájlban és az éles titkokat (például az SQL Database hitelesítő adatait) biztonságosan megtarthatja az App Service-ben. Ugyanez a kód a fejlesztési beállításokat használja, ha helyihiba, és használja az éles titok, amikor az Azure-ba üzembe helyezett.

Más nyelvi halmok esetében jobb, ha [inkább az alkalmazásbeállításokat](#configure-app-settings) használja, mivel a kapcsolati karakterláncok speciális formázást igényelnek a változókulcsokban az értékek eléréséhez. Íme azonban egy kivétel: bizonyos Azure-adatbázistípusokról az alkalmazással együtt készül biztonsági másolatot, ha konfigurálja a kapcsolati karakterláncaikat az alkalmazásban. További információ: [Mi történik biztonsági másolatoval.](manage-backup.md#what-gets-backed-up) Ha nincs szüksége erre az automatikus biztonsági mentésre, használja az alkalmazásbeállításokat.

Futásidőben a kapcsolati karakterláncok környezeti változóként érhetők el, a következő kapcsolattípusokkal előtaggal:

* SQLServer:`SQLCONNSTR_`  
* Mysql:`MYSQLCONNSTR_` 
* SQLAzure:`SQLAZURECONNSTR_` 
* Egyéni:`CUSTOMCONNSTR_`
* Postgresql:`POSTGRESQLCONNSTR_`  

Például egy *ConnectionString1* nevű MySql kapcsolati karakterlánc környezeti `MYSQLCONNSTR_connectionString1`változóként érhető el. A nyelv-verem specifikus lépéseket lásd:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Egyéni tárolók](containers/configure-custom-container.md#configure-environment-variables)

A kapcsolati karakterláncok tárolásakor mindig titkosítva vannak (inketttitkosítás esetén).

> [!NOTE]
> A kapcsolati karakterláncok a Key [Vault-hivatkozások](app-service-key-vault-references.md)használatával is feloldhatók a [Key Vaultból.](/azure/key-vault/)

### <a name="show-hidden-values"></a>Rejtett értékek megjelenítése

Alapértelmezés szerint a kapcsolati karakterláncok értékei biztonsági okokból rejtve maradnak a portálon. A kapcsolati karakterlánc rejtett értékének megtekintéséhez kattintson a karakterlánc **Érték** mezőjére. Az összes kapcsolati karakterlánc értékének megtekintéséhez kattintson az **Érték megjelenítése** gombra.

### <a name="add-or-edit"></a>Hozzáadás vagy szerkesztés

Új kapcsolati karakterlánc hozzáadásához kattintson **az Új kapcsolati karakterlánc gombra.** A párbeszédablakban [a kapcsolati karakterláncot az aktuális foglalathoz ragaszthatja.](deploy-staging-slots.md#which-settings-are-swapped)

A beállítások szerkesztéséhez kattintson a jobb oldalon található **Szerkesztés** gombra.

Ha végzett, kattintson a **Frissítés gombra.** Ne felejtsen el a **Mentés** gombra kattintani a **Konfiguráció** lapon.

### <a name="edit-in-bulk"></a>Szerkesztés ömlesztve

Ha tömegesen szeretne kapcsolati karakterláncokat hozzáadni vagy fűzni, kattintson a **Speciális szerkesztés** gombra. Ha végzett, kattintson a **Frissítés gombra.** Ne felejtsen el a **Mentés** gombra kattintani a **Konfiguráció** lapon.

A kapcsolati karakterláncok json formázása a következő:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Általános beállítások konfigurálása

Az [Azure Portalon]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **Konfiguráció** > **általános beállításai lehetőséget.**

![Általános beállítások](./media/configure-common/open-general.png)

Itt konfigurálhat néhány általános beállítást az alkalmazáshoz. Egyes beállítások megkövetelik, hogy [magasabb tarifacsomagokra skálázva.](manage-scale-up.md)

- **Verembeállítások**: Az alkalmazás futtatásához használt szoftververem, beleértve a nyelvet és az SDK-verziókat. Linux-alkalmazások és egyéni tárolóalkalmazások esetén opcionális indítási parancsot vagy fájlt is beállíthat.
- **Platform beállítások**: Lehetővé teszi a beállítások konfigurálását a tárhelyplatformhoz, beleértve a következőket:
    - **Bitness**: 32 vagy 64 bites.
    - **WebSocket protokoll**: Például [ASP.NET SignalR] vagy [socket.io](https://socket.io/)esetén.
    - **Mindig be:** Tartsa az alkalmazást betöltve akkor is, ha nincs forgalom. Ez szükséges a folyamatos WebJobs vagy WebJobs, amelyek cron kifejezéssel aktiválódnak.
      > [!NOTE]
      > Az Mindig bekapcsolva funkcióval nem szabályozhatja a végpontot. Mindig küld egy kérelmet az alkalmazás gyökér.
    - **Felügyelt csővezeték verziója**: Az IIS [csővezeték-üzemmód]. Állítsa **klasszikusra,** ha olyan örökölt alkalmazással rendelkezik, amelyhez az IIS régebbi verziója szükséges.
    - **HTTP-verzió**: Állítsa **2.0-ra** a [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokoll támogatásának engedélyezéséhez.
    > [!NOTE]
    > A legtöbb modern böngésző csak a TLS protokollt támogatja, míg a nem titkosított forgalom továbbra is a HTTP/1.1 protokollt használja. Annak érdekében, hogy az ügyfélböngészők HTTP/2-vel kapcsolódjanak az alkalmazáshoz, biztosítsa az egyéni DNS-nevet. További információ: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel az Azure App Service szolgáltatásban című témakörben.](configure-ssl-bindings.md)
    - **ARR affinitás:** Többpéldányos telepítés esetén győződjön meg arról, hogy az ügyfél a munkamenet élettartama alatt ugyanarra a példányra van irányítva. Ezt a beállítást **állapotmentes** alkalmazásokesetén kikapcsolva ként állíthatja be.
- **Hibakeresés:** Távoli hibakeresés engedélyezése [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)vagy [Node.js](containers/configure-language-nodejs.md#debug-remotely) alkalmazásokhoz. Ez a beállítás 48 óra elteltével automatikusan kikapcsol.
- **Bejövő ügyféltanúsítványok:** ügyféltanúsítványokat igényel a [kölcsönös hitelesítésben.](app-service-web-configure-tls-mutual-auth.md)

## <a name="configure-default-documents"></a>Alapértelmezett dokumentumok konfigurálása

Ez a beállítás csak windowsos alkalmazásokra van.

Az [Azure Portalon]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **Konfigurációs** > **alapértelmezett dokumentumok**lehetőséget.

![Alapértelmezett dokumentumok](./media/configure-common/open-documents.png)

Az alapértelmezett dokumentum az a weboldal, amely egy webhely gyökér URL-címén jelenik meg. A rendszer a lista első egyező fájlját használja. Új alapértelmezett dokumentum hozzáadásához kattintson az **Új dokumentum gombra.** Ne felejtsen el a Mentés gombra **kattintani.**

Ha az alkalmazás olyan modulokat használ, amelyek az URL-cím alapján irányítják a statikus tartalom kiszolgálása helyett, nincs szükség alapértelmezett dokumentumokra.

## <a name="configure-path-mappings"></a>Elérési út hozzárendelésének konfigurálása

Az [Azure Portalon]keresse meg és válassza az **App Services**lehetőséget, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **Konfigurációs** > **útvonal-hozzárendelések lehetőséget.**

![Görbeleképezések](./media/configure-common/open-path.png)

A **Path mappings (Path mappings)** lap az operációs rendszer típusától függően különböző dolgokat jelenít meg.

### <a name="windows-apps-uncontainerized"></a>Windows-alkalmazások (nem konténerezett)

Windows-alkalmazások esetén testreszabhatja az IIS-kezelő leképezéseit, valamint a virtuális alkalmazásokat és könyvtárakat.

A kezelői hozzárendelések lehetővé teszik egyéni parancsfájl-feldolgozók hozzáadását az adott fájlkiterjesztések reklamálásának kezeléséhez. Egyéni kezelő hozzáadásához kattintson az **Új kezelő gombra.** Állítsa be a kezelőt az alábbiak szerint:

- **Kiterjesztés**. A kezelni kívánt fájlkiterjesztés, például * \*.php* vagy *handler.fcgi*.
- **Parancsfájl-feldolgozó**. A parancsfájlfeldolgozó abszolút elérési útja. A fájlkiterjesztésnek megfelelő fájlokra vonatkozó kérelmeket a parancsfájlfeldolgozó dolgozza fel. Az elérési `D:\home\site\wwwroot` út segítségével hivatkozhat az alkalmazás gyökérkönyvtárára.
- **Argumentumok**. A parancsfájlfeldolgozó választható parancssori argumentumai.

Minden alkalmazás rendelkezik az`/`alapértelmezett gyökérelérési `D:\home\site\wwwroot`úttal ( ) , amelyhez alapértelmezés szerint a kód telepítve van. Ha az alkalmazásgyökér egy másik mappában van, vagy ha a tárház egynél több alkalmazással rendelkezik, itt szerkesztheti vagy hozzáadhatvirtuális alkalmazásokat és könyvtárakat. Kattintson **az Új virtuális alkalmazás vagy könyvtár**elemre.

A virtuális alkalmazások és könyvtárak konfigurálásához adja meg az egyes virtuális`D:\home`könyvtárakat és a hozzájuk tartozó fizikai elérési utat a webhely gyökérkönyvtárához ( ). Szükség esetén az **Alkalmazás** jelölőnégyzet bejelölésére a virtuális könyvtárat alkalmazásként jelölheti meg.

### <a name="containerized-apps"></a>Konténeres alkalmazások

A [tárolóba kért alkalmazáshoz egyéni tárhelyet adhat hozzá.](containers/how-to-serve-content-from-azure-storage.md) A tárolóba illesztett alkalmazások tartalmazzák az összes Linux-alkalmazást, valamint az App Service-en futó Windows- és Linux-egyéni tárolókat. Kattintson **az Új Azure Storage-csatlakoztatás elemre,** és az alábbiak szerint konfigurálja az egyéni tárhelyet:

- **Név**: A megjelenítendő név.
- **Konfigurációs beállítások**: **Alapszintű** vagy **Speciális**.
- **Tárfiókok:** A storage-fiók a kívánt tárolóval.
- **Tárhely típusa**: **Azure Blobs** vagy **Azure Files**.
  > [!NOTE]
  > A Windows tárolóalkalmazások csak az Azure Files-t támogatják.
- **Tárolótároló:** Az alapkonfigurációhoz a kívánt tároló.
- **Megosztási név**: A speciális konfigurációhoz a fájlmegosztás neve.
- **Hozzáférési kulcs**: A speciális konfigurációhoz a hozzáférési kulcs.
- **Csatlakoztatási útvonal**: Az egyéni tároló csatlakoztatásának abszolút elérési útja a tárolóban.

További információ: [Azure Storage-tartalom kiszolgálása a Linuxos App Service-ben](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Nyelvi verembeállítások konfigurálása

Linux-alkalmazásokról:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Egyéni tárolók konfigurálása

Lásd: [Egyéni Linux-tároló konfigurálása az Azure App Service-hez](containers/configure-custom-container.md)

## <a name="next-steps"></a>További lépések

- [Egyéni tartománynév konfigurálása az Azure App Service-ben]
- [Átmeneti környezetek beállítása az Azure App Service-ben]
- [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel az Azure App Service-ben](configure-ssl-bindings.md)
- [Diagnosztikai naplók engedélyezése](troubleshoot-diagnostic-logs.md)
- [Alkalmazás méretezése az Azure App Service-ben]
- [Az Azure App Service alapvető tudnivalói]
- [Az applicationHost.config beállításainak módosítása az applicationHost.xdt fájllal](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET jeljelző]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Egyéni tartománynév konfigurálása az Azure App Service-ben]: ./app-service-web-tutorial-custom-domain.md
[Átmeneti környezetek beállítása az Azure App Service-ben]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Az Azure App Service alapvető tudnivalói]: ./web-sites-monitor.md
[folyamat mód]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Alkalmazás méretezése az Azure App Service-ben]: ./manage-scale-up.md
