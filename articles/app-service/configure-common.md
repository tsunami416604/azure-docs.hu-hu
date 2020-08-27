---
title: Alkalmazások konfigurálása a portálon
description: Megtudhatja, hogyan konfigurálhat egy App Service alkalmazás általános beállításait a Azure Portal. Alkalmazásbeállítások, kapcsolatok karakterláncai, platform, nyelvi verem, tároló stb.
keywords: Azure app Service, webalkalmazás, Alkalmazásbeállítások, környezeti változók
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 4990862bacbf2afe2d4b5f39c2b9d31a7c48b78e
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962893"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>App Service alkalmazás konfigurálása a Azure Portal

Ez a témakör azt ismerteti, hogyan konfigurálható a webalkalmazások, a mobil háttérrendszer vagy az API-alkalmazás általános beállításai a [Azure Portal]használatával.

## <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása

App Service az Alkalmazásbeállítások a környezeti változókként átadott változók az alkalmazás kódjába. A Linux-alkalmazások és az egyéni tárolók esetében a App Service a jelző használatával átadja az alkalmazás beállításait a tárolóhoz a `--env` környezeti változónak a tárolóban történő beállításához.

A [Azure Portal]keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. 

![App Services keresése](./media/configure-common/search-for-app-services.png)

Az alkalmazás bal oldali menüjében válassza a **konfigurációs**  >  **alkalmazás beállításai**lehetőséget.

![Alkalmazásbeállítások](./media/configure-common/open-ui.png)

A ASP.NET és a ASP.NET Core fejlesztők esetében az Alkalmazásbeállítások beállítása a App Serviceban hasonló, mint `<appSettings>` a *Web.config* vagy a *appsettings.js*be értékének beállítása, de a app Serviceban szereplő értékek felülbírálják *Web.config* vagy *appsettings.js*. Megtarthatja a fejlesztési beállításokat (például a helyi MySQL-jelszót) *Web.config* vagy *appsettings.js*, de az üzemi titkok (például az Azure MySQL-adatbázis jelszava) biztonságosak a app Serviceban. Ugyanez a kód a helyi hibakeresés során a fejlesztési beállításokat használja, és az Azure-ba való üzembe helyezéskor az éles titkot használja.

Más nyelvi stackekhez hasonlóan az Alkalmazásbeállítások környezeti változókként is beszerezhetők futásidőben. A nyelvspecifikus speciális lépéseiért lásd:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Egyéni tárolók](configure-custom-container.md#configure-environment-variables)

Az Alkalmazásbeállítások mindig titkosítva tárolódnak (titkosított – REST).

> [!NOTE]
> Az Alkalmazásbeállítások [Key Vault referenciák](app-service-key-vault-references.md)használatával is feloldhatók [Key Vault](../key-vault/index.yml) .

### <a name="show-hidden-values"></a>Rejtett értékek megjelenítése

Alapértelmezés szerint az Alkalmazásbeállítások értékei rejtettek a portálon a biztonság érdekében. Az Alkalmazásbeállítások rejtett értékének megtekintéséhez kattintson a beállítás **Value (érték** ) mezőjére. Az Alkalmazásbeállítások értékének megjelenítéséhez kattintson az **érték megjelenítése** gombra.

### <a name="add-or-edit"></a>Hozzáadás vagy szerkesztés

Új alkalmazás-beállítás hozzáadásához kattintson az **új Alkalmazásbeállítás**elemre. A párbeszédpanelen a [beállítást a jelenlegi tárolóhelyre is lehet ragasztani](deploy-staging-slots.md#which-settings-are-swapped).

A beállítások szerkesztéséhez kattintson a jobb oldalon található **Szerkesztés** gombra.

Ha elkészült, kattintson a **frissítés**gombra. Ne felejtse el, hogy a **konfiguráció** lapon a **Mentés** vissza gombra kattint.

> [!NOTE]
> Egy alapértelmezett Linux-tárolóban vagy egy egyéni Linux-tárolóban az alkalmazás-beállítás nevének bármely beágyazott JSON-kulcsának struktúráját `ApplicationInsights:InstrumentationKey` app Service kell konfigurálni `ApplicationInsights__InstrumentationKey` a kulcs neveként. Más szóval, minden esetben a `:` következőt kell cserélnie `__` (dupla aláhúzás).
>

### <a name="edit-in-bulk"></a>Szerkesztés tömegesen

Ha tömegesen szeretné felvenni vagy szerkeszteni az alkalmazás beállításait, kattintson a **Speciális szerkesztés** gombra. Ha elkészült, kattintson a **frissítés**gombra. Ne felejtse el, hogy a **konfiguráció** lapon a **Mentés** vissza gombra kattint.

Az Alkalmazásbeállítások a következő JSON-formátummal rendelkeznek:

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

A [Azure Portal]keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **konfigurációs**  >  **alkalmazás beállításai**lehetőséget.

![Alkalmazásbeállítások](./media/configure-common/open-ui.png)

A ASP.NET és a ASP.NET Core fejlesztők számára a App Service a kapcsolatok karakterláncának beállítása, például `<connectionStrings>` a *Web.configban *való beállítása, de a app Serviceban megadott értékek felülbírálják a *Web.config*. A fejlesztési beállításokat (például egy adatbázisfájlt) a *Web.config* és a termelési titkokban (például SQL Database hitelesítő adatokban) biztonságosan megtarthatja app Service. Ugyanez a kód a helyi hibakeresés során a fejlesztési beállításokat használja, és az Azure-ba való üzembe helyezéskor az éles titkot használja.

Más nyelvi stackek esetében érdemes inkább az [Alkalmazásbeállítások](#configure-app-settings) használatát használni, mert a kapcsolati karakterláncok speciális formázást igényelnek a változó kulcsokban az értékek eléréséhez. Íme egy kivétel, azonban bizonyos Azure-adatbázisok biztonsági mentése az alkalmazással együtt történik, ha az alkalmazásban konfigurálja a kapcsolatok karakterláncait. További információ: [Mi történik a biztonsági mentésben](manage-backup.md#what-gets-backed-up). Ha nincs szüksége erre az automatikus biztonsági mentésre, használja az Alkalmazásbeállítások szolgáltatást.

Futásidőben a kapcsolatok karakterláncai környezeti változókként érhetők el, és a következő kapcsolattípus-típusokkal vannak meghatározva:

* SQLServer `SQLCONNSTR_`  
* MySQL `MYSQLCONNSTR_` 
* Rendszerkarbantartás `SQLAZURECONNSTR_` 
* Egyéni `CUSTOMCONNSTR_`
* PostgreSQL `POSTGRESQLCONNSTR_`  

Egy *connectionstring1* nevű MySQL-kapcsolati sztring például környezeti változóként érhető el `MYSQLCONNSTR_connectionString1` . A nyelvspecifikus speciális lépéseiért lásd:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Egyéni tárolók](configure-custom-container.md#configure-environment-variables)

A kapcsolódási karakterláncok mindig titkosítva tárolódnak (titkosított – REST).

> [!NOTE]
> A kapcsolatok karakterláncai a [Key Vault](../key-vault/index.yml) [Key Vault hivatkozásokkal](app-service-key-vault-references.md)is feloldhatók.

### <a name="show-hidden-values"></a>Rejtett értékek megjelenítése

Alapértelmezés szerint a rendszer elrejti a kapcsolatok karakterláncának értékeit a portálon a biztonság érdekében. A kapcsolódási karakterlánc rejtett értékének megjelenítéséhez egyszerűen kattintson a karakterlánc **Value (érték** ) mezőjére. Az összes kapcsolati karakterlánc értékének megtekintéséhez kattintson az **érték megjelenítése** gombra.

### <a name="add-or-edit"></a>Hozzáadás vagy szerkesztés

Új kapcsolódási karakterlánc hozzáadásához kattintson az **új kapcsolódási karakterlánc**elemre. A párbeszédpanelen a [kapcsolódási karakterláncot a jelenlegi bővítőhelyre lehet ragasztani](deploy-staging-slots.md#which-settings-are-swapped).

A beállítások szerkesztéséhez kattintson a jobb oldalon található **Szerkesztés** gombra.

Ha elkészült, kattintson a **frissítés**gombra. Ne felejtse el, hogy a **konfiguráció** lapon a **Mentés** vissza gombra kattint.

### <a name="edit-in-bulk"></a>Szerkesztés tömegesen

Tömeges kapcsolódási karakterláncok hozzáadásához vagy szerkesztéséhez kattintson a **Speciális szerkesztés** gombra. Ha elkészült, kattintson a **frissítés**gombra. Ne felejtse el, hogy a **konfiguráció** lapon a **Mentés** vissza gombra kattint.

A kapcsolatok sztringje a következő JSON-formátummal rendelkezik:

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

A [Azure Portal]keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **konfiguráció**  >  **általános beállítások**lehetőséget.

![Általános beállítások](./media/configure-common/open-general.png)

Itt konfigurálhatja az alkalmazás egyes gyakori beállításait. Néhány beállításhoz a [magasabb díjszabású csomagok skálázása](manage-scale-up.md)szükséges.

- **Verem beállításai**: az alkalmazás futtatásához használt szoftver, beleértve a nyelv és az SDK verzióját is. A Linux-alkalmazások és az egyéni tárolós alkalmazások esetében opcionális indítási parancsot vagy fájlt is beállíthat.
- **Platform beállításai**: lehetővé teszi az üzemeltetési platform beállításainak konfigurálását, beleértve a következőket:
    - **Bitszáma**: 32 bites vagy 64 bites.
    - **WebSocket protokoll**: [ASP.net-jelzőhöz] vagy [socket.IO](https://socket.io/), például.
    - **Always On**: megtartja az alkalmazás betöltését akkor is, ha nincs forgalom. Szükség van a folyamatos webjobs-feladatokra, illetve a CRON-kifejezéssel aktivált webjobs-feladatokra.
      > [!NOTE]
      > Az Always On szolgáltatással az előtér-terheléselosztó kérelmet küld az alkalmazás gyökerének. Nem lehet konfigurálni a App Service alkalmazás-végpontját.
    - **Felügyelt folyamat verziója**: az IIS- [folyamat mód]. Állítsa **klasszikusra** , ha olyan örökölt alkalmazással rendelkezik, amelyhez az IIS régebbi verziója szükséges.
    - **Http-verzió**: állítsa **2,0** értékre a [https/2](https://wikipedia.org/wiki/HTTP/2) protokoll támogatásának engedélyezéséhez.
    > [!NOTE]
    > A legtöbb modern böngésző csak a TLS protokollal támogatja a HTTP/2 protokollt, a nem titkosított forgalom pedig továbbra is HTTP/1.1-et használ. Győződjön meg arról, hogy az ügyféloldali böngészők a HTTP/2 protokollal csatlakoznak az alkalmazáshoz, és biztonságossá teszi az egyéni DNS-nevet. További információ: [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure app Serviceban](configure-ssl-bindings.md).
    - **ARR affinitás**: többpéldányos telepítés esetén győződjön meg arról, hogy az ügyfél ugyanahhoz a példányhoz van irányítva a munkamenet élettartama szempontjából. Ezt a beállítást **kikapcsolhatja** az állapot nélküli alkalmazások esetében.
- **Hibakeresés**: engedélyezze a távoli hibakeresést [ASP.net](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.net Core](/visualstudio/debugger/remote-debugging-azure)vagy [Node.js](configure-language-nodejs.md#debug-remotely) alkalmazásokhoz. Ez a beállítás 48 óra elteltével automatikusan kikapcsol.
- **Bejövő Ügyféltanúsítványok**: ügyféltanúsítvány megkövetelése [kölcsönös hitelesítéssel](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Alapértelmezett dokumentumok konfigurálása

Ez a beállítás csak Windows-alkalmazásokhoz használható.

A [Azure Portal]keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **konfiguráció**  >  **alapértelmezett dokumentumok**elemet.

![Alapértelmezett dokumentumok](./media/configure-common/open-documents.png)

Az alapértelmezett dokumentum a webhely gyökerének URL-címében megjelenő weblap. A rendszer a lista első megfelelő fájlját használja. Új alapértelmezett dokumentum hozzáadásához kattintson az **új dokumentum**elemre. Ne felejtse el a **Mentés**gombra kattintani.

Ha az alkalmazás olyan modulokat használ, amelyek az URL-cím alapján statikus tartalom kiszolgálása helyett az URL-címet használják, az alapértelmezett dokumentumok nem szükségesek.

## <a name="configure-path-mappings"></a>Elérésiút-megfeleltetések konfigurálása

A [Azure Portal]keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. Az alkalmazás bal oldali menüjében válassza a **konfigurációs**  >  **útvonal-hozzárendelések**lehetőséget.

![Elérésiút-megfeleltetések](./media/configure-common/open-path.png)

Az **elérési út leképezése** lapon az operációs rendszer típusa alapján különböző dolgok láthatók.

### <a name="windows-apps-uncontainerized"></a>Windows-alkalmazások (nem tároló)

Windows-alkalmazások esetén testreszabhatja az IIS-kezelő leképezéseit és a virtuális alkalmazásokat és címtárakat.

A kezelő-hozzárendelések lehetővé teszik egyéni parancsfájl-feldolgozók hozzáadását az adott fájlkiterjesztések kéréseinek kezeléséhez. Egyéni kezelő hozzáadásához kattintson az **új kezelő**elemre. Konfigurálja a kezelőt a következőképpen:

- **Bővítmény**. A kezelni kívánt fájlkiterjesztés, például * \* . php* vagy *Handler. fcgi*.
- **Parancsfájl-feldolgozó**. A parancsfájl-feldolgozó abszolút elérési útja. A fájlkiterjesztés által megegyező fájlokra irányuló kérelmeket a parancsfájl-feldolgozó dolgozza fel. Az elérési út használatával `D:\home\site\wwwroot` tekintse meg az alkalmazás gyökérkönyvtárát.
- **Argumentumok**. Nem kötelező parancssori argumentumok a parancsfájl-feldolgozóhoz.

Mindegyik alkalmazáshoz az alapértelmezett gyökérszintű elérési út ( `/` ) van rendelve `D:\home\site\wwwroot` , ahol a kód alapértelmezés szerint telepítve van. Ha az alkalmazás gyökérkönyvtára egy másik mappában található, vagy ha a tárház több alkalmazással is rendelkezik, akkor itt szerkesztheti vagy adhatja hozzá a virtuális alkalmazásokat és címtárakat. Kattintson az **új virtuális alkalmazás vagy könyvtár**elemre.

A virtuális alkalmazások és könyvtárak konfigurálásához adja meg az összes virtuális könyvtárat és a hozzá tartozó fizikai elérési utat a webhely gyökeréhez () viszonyítva `D:\home` . Ha szeretné, bejelölheti az **alkalmazás** jelölőnégyzetet is, ha a virtuális könyvtárat alkalmazásként szeretné megjelölni.

### <a name="containerized-apps"></a>Tároló alkalmazások

[A tároló alkalmazáshoz egyéni tárolót is hozzáadhat](configure-connect-to-azure-storage.md). A tároló alkalmazások közé tartozik az összes Linux-alkalmazás, valamint a Windows-és Linux-alapú egyéni tárolók is, amelyek a App Serviceon futnak. Kattintson az **új Azure Storage-csatlakoztatás** lehetőségre, és konfigurálja az egyéni tárolót az alábbiak szerint:

- **Name (név**): a megjelenítendő név.
- **Konfigurációs beállítások**: **alapszintű** vagy **speciális**.
- **Storage-fiókok**: a kívánt tárolóval rendelkező Storage-fiók.
- **Tárolás típusa**: **Azure-blobok** vagy **Azure Files**.
  > [!NOTE]
  > A Windows Container apps csak Azure Files támogatja.
- **Storage-tároló**: az alapszintű konfigurációhoz a kívánt tároló.
- **Megosztás neve**: Speciális konfiguráció esetén a fájlmegosztás neve.
- **Hozzáférési kulcs**: Speciális konfiguráció esetén a hozzáférési kulcs.
- **Csatlakoztatási útvonal**: a tároló abszolút elérési útja az egyéni tároló csatlakoztatásához.

További információ: az [Azure Storage hálózati megosztásként való elérése app Service-tárolóban](configure-connect-to-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Nyelvi verem beállításainak konfigurálása

Linux-alkalmazások esetén lásd:

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Egyéni tárolók konfigurálása

Lásd: [Egyéni Linux-tároló konfigurálása Azure app Servicehoz](configure-custom-container.md)

## <a name="next-steps"></a>Következő lépések

- [Egyéni tartománynév konfigurálása Azure App Service]
- [Átmeneti környezetek beállítása az Azure App Service-ben]
- [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel Azure App Service](configure-ssl-bindings.md)
- [Diagnosztikai naplók engedélyezése](troubleshoot-diagnostic-logs.md)
- [Alkalmazás méretezése Azure App Service]
- [A Azure App Service figyelésének alapjai]
- [applicationHost.config beállítások módosítása a applicationHost. XDT](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET-jelző]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Egyéni tartománynév konfigurálása Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Átmeneti környezetek beállítása az Azure App Service-ben]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[A Azure App Service figyelésének alapjai]: ./web-sites-monitor.md
[folyamat mód]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Alkalmazás méretezése Azure App Service]: ./manage-scale-up.md