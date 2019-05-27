---
title: A portálban – az Azure App Service-alkalmazások konfigurálása
description: Ismerkedjen meg az App Service-alkalmazások általános beállításainak konfigurálása az Azure Portalon.
keywords: az Azure app Service-ben, webalkalmazás, beállítások, a környezeti változók
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: bcc970375120f76e4ec8a90f487d251296f92dba
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957910"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>App Service-alkalmazások konfigurálása az Azure Portalon

Ez a témakör azt ismerteti, hogyan web apps, mobil háttérrendszer vagy API-alkalmazás használatával általános beállításainak konfigurálása a [Azure Portal].

## <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása

Az App Service-ben például a környezeti változók alkalmazásbeállításokat kell használnia. Az a [Azure Portal], keresse meg az alkalmazás lapját. Az alkalmazás bal oldali menüben kattintson a **konfigurációs** > **Alkalmazásbeállítások**.

![Alkalmazásbeállítások](./media/configure-common/open-ui.png)

Az ASP.NET és az ASP.NET Core-fejlesztőknek beállítás Alkalmazásbeállítások az App Service-ben hasonlóak állítja be őket `<appSettings>` a *Web.config*, de az értékeket az App Service-ben, bírálja felül az *Web.config*. Fejlesztői beállítások (pl. helyi MySQL-jelszó) megmaradhat *Web.config*, de éles titkos kulcsokat (például az Azure-beli MySQL database jelszó) biztonságos App Service-ben. Ugyanazt a kódot a fejlesztési beállításokat használja, amikor helyileg hibakeresést, és az éles titkos kulcsok az Azure-ban üzembe helyezett használ.

Egyéb nyelvi implementációk hasonlóképpen lekérése az alkalmazásbeállítások környezeti változókként futásidőben. Nyelvi-verem adott lépéseiért lásd:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Egyéni tárolók](containers/configure-custom-container.md#configure-environment-variables)

Alkalmazásbeállítások mindig titkosított tárolt (titkosított inaktív).

> [!NOTE]
> Alkalmazásbeállítások is feloldható a [Key Vault](/azure/key-vault/) használatával [Key Vault hivatkozik](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Rejtett értékek megjelenítése

Az alkalmazásbeállítások értékeit alapértelmezés szerint rejtve vannak a biztonság a portálon. Az Alkalmazásbeállítás rejtett érték megtekintéséhez kattintson a **érték** mezőjét, ennek a beállításnak. Minden alkalmazás-beállítás értékének megtekintéséhez kattintson a **érték megjelenítése** gombra.

### <a name="add-or-edit"></a>Hozzáadása vagy szerkesztése

Adjon hozzá egy új alkalmazásbeállítást, kattintson a **új Alkalmazásbeállítás**. A párbeszédpanelen is [elgondolkodni a beállítást, a jelenlegi tárolóhelyen megadott](deploy-staging-slots.md#which-settings-are-swapped).

Egy beállítás szerkesztéséhez kattintson a **szerkesztése** gombra a jobb oldalon.

Ha befejezte, kattintson a **frissítés**. Ne felejtse el kattintson **mentése** térjen vissza a **konfigurációs** lapot.

> [!NOTE]
> Alapértelmezett Linux-tárolóban vagy egy egyéni Linux-tárolón, bármilyen beágyazott JSON struktúra az azon alkalmazásbeállítás neve, például `ApplicationInsights:InstrumentationKey` kell konfigurálni az App Service-t `ApplicationInsights__InstrumentationKey` a kulcs neve. Más szóval bármely `:` kell helyettesíteni `__` (dupla aláhúzásjelet lehet).
>

### <a name="edit-in-bulk"></a>Tömeges szerkesztése

Adja hozzá, vagy egyszerre több alkalmazás beállításainak szerkesztése, kattintson a **Szerkesztés speciális** gombra. Ha befejezte, kattintson a **frissítés**. Ne felejtse el kattintson **mentése** térjen vissza a **konfigurációs** lapot.

Alkalmazásbeállítások van, a következő JSON-formátum:

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

Az a [Azure Portal], keresse meg az alkalmazás lapját. Az alkalmazás bal oldali menüben kattintson a **konfigurációs** > **Alkalmazásbeállítások**.

![Alkalmazásbeállítások](./media/configure-common/open-ui.png)

Az ASP.NET és az ASP.NET Core-fejlesztőknek beállítás kapcsolati karakterláncok az App Service-ben hasonlóak állítja be őket `<connectionStrings>` a *Web.config*, de az App Service-ben megadott értékeket, bírálja felül az *Web.config*. Fejlesztői beállítások (például egy adatbázis-fájl) megmaradhat *Web.config* és éles titkos kulcsokat (például SQL-adatbázis-hitelesítő adatok) biztonságos App Service-ben. Ugyanazt a kódot a fejlesztési beállításokat használja, amikor helyileg hibakeresést, és az éles titkos kulcsok az Azure-ban üzembe helyezett használ.

Az egyéb nyelvi implementációk célszerűbb használata [Alkalmazásbeállítások](#configure-app-settings) , mert kapcsolati karakterláncok igényelnek speciális formázás változó kulcsainak order eléréséhez az értékeket. Itt azonban az egyetlen kivétel van: az Azure-adatbázis bizonyos készül biztonsági másolat együtt az alkalmazás a kapcsolati karakterláncok az alkalmazás konfigurálásakor. További információkért lásd: [mi mentendő](manage-backup.md#what-gets-backed-up). Ha már nincs szüksége az automatikus biztonsági mentés, majd használja a beállításokat.

Futásidőben a kapcsolati karakterláncok előzi meg a következő kapcsolattípusok közül a környezeti változókként érhetők el:

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL-adatbázis: `SQLAZURECONNSTR_`
* Egyéni: `CUSTOMCONNSTR_`

A MySql-kapcsolati karakterlánc neve például *connectionstring1* érhetik a környezeti változó `MYSQLCONNSTR_connectionString1`. Nyelvi-verem adott lépéseiért lásd:

- [ASP.NET Core](containers/configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](containers/configure-language-nodejs.md#access-environment-variables)
- [PHP](containers/configure-language-php.md#access-environment-variables)
- [Python](containers/how-to-configure-python.md#access-environment-variables)
- [Java](containers/configure-language-java.md#data-sources)
- [Ruby](containers/configure-language-ruby.md#access-environment-variables)
- [Egyéni tárolók](containers/configure-custom-container.md#configure-environment-variables)

Kapcsolati karakterláncok mindig titkosított tárolt (titkosított inaktív).

> [!NOTE]
> Kapcsolati karakterláncok is feloldható a [Key Vault](/azure/key-vault/) használatával [Key Vault hivatkozik](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Rejtett értékek megjelenítése

Kapcsolati karakterláncok értékeit alapértelmezés szerint rejtve vannak a biztonság a portálon. Tekintse meg a kapcsolati karakterlánc értéke rejtett, csak kattintson a **érték** mezőjét, mely a karakterláncban. A kapcsolati sztring értékek megtekintéséhez kattintson a **érték megjelenítése** gombra.

### <a name="add-or-edit"></a>Hozzáadása vagy szerkesztése

Új kapcsolati karakterlánc hozzáadásához kattintson **új kapcsolati karakterlánc**. A párbeszédpanelen is [elgondolkodni a kapcsolati karakterláncot, a jelenlegi tárolóhelyen megadott](deploy-staging-slots.md#which-settings-are-swapped).

Egy beállítás szerkesztéséhez kattintson a **szerkesztése** gombra a jobb oldalon.

Ha befejezte, kattintson a **frissítés**. Ne felejtse el kattintson **mentése** térjen vissza a **konfigurációs** lapot.

### <a name="edit-in-bulk"></a>Tömeges szerkesztése

Adja hozzá, vagy tömegesen kapcsolati karakterláncok szerkesztése, kattintson a **Szerkesztés speciális** gombra. Ha befejezte, kattintson a **frissítés**. Ne felejtse el kattintson **mentése** térjen vissza a **konfigurációs** lapot.

Kapcsolati karakterláncok van, a következő JSON-formátum:

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

Az a [Azure Portal], keresse meg az alkalmazás lapját. Az alkalmazás bal oldali menüben kattintson a **konfigurációs** > **Alkalmazásbeállítások**.

![Általános beállítások](./media/configure-common/open-general.png)

Itt az alkalmazás néhány gyakori beállításai is konfigurálhatja. Egyes beállítások megkövetelik a [vertikális felskálázás legfeljebb magasabb szintű tarifacsomagban is kapható](web-sites-scale.md).

- **A verem beállítások**: Futtassa az alkalmazást, beleértve a nyelv és az SDK-verziókra szoftververmeket. A Linux-alkalmazások és egyéni tároló alkalmazás egy nem kötelező indítási parancs vagy -fájlt is beállíthat.
- **Platformbeállításai**: Lehetővé teszi, hogy konfigurálhatja a beállításait, a üzemeltetési platform, többek között:
    - **Bitszámértékének**: 32 bites vagy 64 bites.
    - **WebSocket protokoll**: A [ASP.NET SignalR] vagy [a Socket.IO kódtár](https://socket.io/), például.
    - **Always On**: Tartsa meg az alkalmazás akkor is, amikor nincs adatforgalom. Engedélyezze a folyamatos webjobs-feladatok, vagy aktivált egy CRON-kifejezés használata a WebJobs kell.
    - **Kezelt Folyamatverzió**: Az IIS [folyamatkezelési mód]. Állítsa be **klasszikus** Ha egy örökölt alkalmazás, amely az IIS egy régebbi verziója szükséges.
    - **HTTP-verzió**: Állítsa be **2.0** támogatásának engedélyezéséhez [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) protokollt.
    > [!NOTE]
    > Legtöbb modern böngésző támogatja a HTTP/2 protokoll TLS protokollon keresztüli csak, miközben továbbra is használja a HTTP/1.1-es nem titkosított forgalmat. Győződjön meg arról, hogy az ügyfél böngészők csatlakoznia kell a HTTP/2, alkalmazások vagy [egy App Service-tanúsítvány vásárlása](web-sites-purchase-ssl-web-site.md) az alkalmazás egyéni tartomány vagy [harmadik féltől származó tanúsítvány kötése](app-service-web-tutorial-custom-ssl.md).
    - **Az ARR-affinitás**: Többpéldányos központi telepítés győződjön meg arról, hogy az ügyfél lesz irányítva a munkamenet élettartamára ugyanezen példányában. És ez a beállítás megadható **ki** állapot nélküli alkalmazások számára.
- **Hibakeresés**: A távoli hibakeresés engedélyezése [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure), vagy [Node.js](containers/configure-language-nodejs.md#debug-remotely) alkalmazásokat. 48 óra múlva automatikusan kikapcsolja ezt a beállítást.
- **Bejövő ügyféltanúsítványok**: az ügyféltanúsítványok megköveteléséhez [kölcsönös hitelesítés](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Alapértelmezett dokumentumok beállítása

Ez a beállítás csak a Windows-alkalmazások szól.

Az a [Azure Portal], keresse meg az alkalmazás lapját. Az alkalmazás bal oldali menüben kattintson a **konfigurációs** > **alapértelmezett dokumentumok**.

![Általános beállítások](./media/configure-common/open-documents.png)

Az alapértelmezett dokumentum a weblap, amelyen egy webhely gyökérszintű URL-címen jelenik meg. A lista első egyeztetési fájlt használja. Alapértelmezett dokumentum hozzáadásához kattintson **új dokumentum**. Ne felejtse el kattintson **mentése**.

Ha az alkalmazás URL-cím helyett a statikus tartalmat szolgáltató alapján végző modult használ, nincs az alapértelmezett dokumentumok szükség.

## <a name="configure-path-mappings"></a>Elérési út-leképezések konfigurálása

Az a [Azure Portal], keresse meg az alkalmazás lapját. Az alkalmazás bal oldali menüben kattintson a **konfigurációs** > **elérési út leképezések**.

![Általános beállítások](./media/configure-common/open-path.png)

A **elérési út leképezések** oldal megjeleníti az operációs rendszer típusa alapján különböző dolgok.

### <a name="windows-apps-uncontainerized"></a>Windows-alkalmazások (uncontainerized)

Windows-alkalmazások esetében testre szabhatja az IIS kezelőleképezések és a virtuális alkalmazások és könyvtárak.

Kezelőleképezések segítségével adhat hozzá az egyéni szkriptek processzorok az adott kérések kezelésére. Egyéni kezelő hozzáadásához kattintson **új kezelő**. Konfigurálja a kezelő az alábbiak szerint:

- **Bővítmény**. A fájl kiterjesztése szeretné kezelni, mint például  *\*.php* vagy *handler.fcgi*.
- **Parancsfájl-feldolgozókhoz**. A parancsprogram-feldolgozó Önnek abszolút elérési útja. Fájlok, amelyek megfelelnek a fájlkiterjesztés kérelmeket dolgozza fel a parancsprogram-feldolgozót. Az elérési utat használja `D:\home\site\wwwroot` , tekintse meg az alkalmazás gyökérkönyvtárában.
- **Argumentumok**. Parancssori argumentumokat használni a parancsprogram-feldolgozót.

Minden alkalmazás rendelkezik alapértelmezett gyökér elérési útját (`/`) leképezve `D:\home\site\wwwroot`, amelyben alapértelmezés szerint a kód üzembe helyezése. Ha az alkalmazás gyökér másik mappában van, vagy ha az adattárban több alkalmazás, szerkesztheti, vagy adja hozzá a virtuális alkalmazások és könyvtárak itt. Kattintson a **új virtuális alkalmazás vagy könyvtár**.

Konfigurálhatja a virtuális alkalmazások és könyvtárak, adja meg az egyes virtuális könyvtárakat és az annak megfelelő fizikai elérési út a webhely gyökeréhez viszonyítva (`D:\home`). Másik lehetőségként kiválaszthatja a **alkalmazás** jelölőnégyzetet, hogy egy alkalmazás egy virtuális könyvtárat.

### <a name="containerized-apps"></a>Tárolóalapú alkalmazásokat

Is [adja hozzá a tárolóalapú alkalmazás egyéni tárolási](containers/how-to-serve-content-from-azure-storage.md). Tárolóalapú alkalmazások közé tartoznak az összes Linux-alkalmazások, és az App Service-ben futó Windows és Linux rendszerű egyéni tárolók. Kattintson a **új Azure Storage csatlakoztatása** és az egyéni tároló a következőképpen konfigurálja:

- **Név**: A megjelenített neve.
- **Konfigurációs beállítások**: **Alapszintű** vagy **speciális**.
- **Storage-fiókok**: A storage-fiók a tárolóval szeretne.
- **Tárolási típus**: **Azure-Blobok** vagy **az Azure Files**.
  > [!NOTE]
  > Windows-tárolóalkalmazásokról csak az Azure Files támogatják.
- **A tároló**: Alapszintű konfigurálása, a kívánt tárolót.
- **Megosztási név**: Speciális konfiguráció a fájlmegosztás nevét.
- **Hozzáférési kulcs**: Speciális konfiguráció, a hozzáférési kulcsot.
- **Csatlakoztatási elérési útját**: A tárolóban, az egyéni tároló abszolút elérési útja.

További információkért lásd: [tartalom továbbítása az Azure Storage a linuxon futó App Service-ben](containers/how-to-serve-content-from-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Nyelvi stack beállításainak konfigurálása

Linux-alkalmazások lásd:

- [ASP.NET Core](containers/configure-language-dotnetcore.md)
- [Node.js](containers/configure-language-nodejs.md)
- [PHP](containers/configure-language-php.md)
- [Python](containers/how-to-configure-python.md)
- [Java](containers/configure-language-java.md)
- [Ruby](containers/configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Egyéni tárolók konfigurálása

Lásd: [egyéni Linux-tárolóban konfigurálása az Azure App Service-ben](containers/configure-custom-container.md)

## <a name="next-steps"></a>További lépések

- [Egyéni tartománynév konfigurálása az Azure App Service-ben]
- [Átmeneti környezetek beállítása az Azure App Service-ben]
- [HTTPS engedélyezése az Azure App Service-alkalmazás]
- [Diagnosztikai naplók engedélyezése](troubleshoot-diagnostic-logs.md)
- [Alkalmazások skálázása az Azure App Service-ben]
- [Megfigyelési alapismeretek az Azure App Service-ben]
- [ApplicationHost.xdt az applicationHost.config fájl beállításainak módosítása](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Egyéni tartománynév konfigurálása az Azure App Service-ben]: ./app-service-web-tutorial-custom-domain.md
[Átmeneti környezetek beállítása az Azure App Service-ben]: ./deploy-staging-slots.md
[HTTPS engedélyezése az Azure App Service-alkalmazás]: ./app-service-web-tutorial-custom-ssl.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Megfigyelési alapismeretek az Azure App Service-ben]: ./web-sites-monitor.md
[folyamatkezelési mód]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Alkalmazások skálázása az Azure App Service-ben]: ./web-sites-scale.md
