---
title: TLS-/SSL-tanúsítványok hozzáadása és kezelése
description: Hozzon létre egy ingyenes tanúsítványt, importáljon egy App Service-tanúsítványt, importáljon egy Key Vault-tanúsítványt, vagy vásároljon egy App Service-tanúsítványt az Azure App Service-ben.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 4edf710e575bbb26fb0e247e59ff5c796f16226e
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810588"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>TLS-/SSL-tanúsítvány hozzáadása az Azure App Service-ben

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez a cikk bemutatja, hogyan hozhat létre, tölthet fel vagy importálhat privát tanúsítványt vagy nyilvános tanúsítványt az App Service szolgáltatásba. 

Miután hozzáadta a tanúsítványt az App Service-alkalmazáshoz vagy [függvényalkalmazáshoz,](https://docs.microsoft.com/azure/azure-functions/) [egyéni DNS-nevet is biztosíthat vele,](configure-ssl-bindings.md) vagy [használhatja azt az alkalmazáskódban.](configure-ssl-certificate-in-code.md)

Az alábbi táblázat felsorolja a tanúsítványok App Service-ben való hozzáadásának lehetőségeit:

|Beállítás|Leírás|
|-|-|
| Ingyenes App Service-kezelt tanúsítvány létrehozása (előzetes verzió) | Egy privát tanúsítvány, amely könnyen használható, ha `www` csak az [egyéni tartományvagy](app-service-web-tutorial-custom-domain.md) bármely nem meztelen tartomány biztonságossá kell tenniaz App Service-ben. |
| App-szolgáltatás-tanúsítvány vásárlása | Az Azure által kezelt privát tanúsítvány. Egyesíti az automatizált tanúsítványkezelés egyszerűségét, valamint a megújítási és exportálási lehetőségek rugalmasságát. |
| Tanúsítvány importálása a Key Vaultból | Akkor hasznos, ha [az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) használatával kezeli a [PKCS12 tanúsítványokat.](https://wikipedia.org/wiki/PKCS_12) Lásd: [Privát tanúsítványkövetelmények](#private-certificate-requirements). |
| Privát tanúsítvány feltöltése | Ha már rendelkezik egy külső szolgáltatótól származó magántanúsítvánnyal, feltöltheti azt. Lásd: [Privát tanúsítványkövetelmények](#private-certificate-requirements). |
| Nyilvános tanúsítvány feltöltése | A nyilvános tanúsítványok nem használhatók egyéni tartományok védelmére, de betöltheti őket a kódba, ha szüksége van rájuk a távoli erőforrások eléréséhez. |

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követése:

- [Hozzon létre egy App Service-alkalmazást](/azure/app-service/).
- Csak ingyenes tanúsítvány: rendeljen le `www.contoso.com`egy aldomaint (például ) az App Service-hez [CNAME rekorddal.](app-service-web-tutorial-custom-domain.md#map-a-cname-record)

## <a name="private-certificate-requirements"></a>Magántanúsítványra vonatkozó követelmények

> [!NOTE]
> Az Azure Web Apps **nem** támogatja az AES256-ot, és minden pfx fájlt tripleDES-sel kell titkosítani.

Az [ingyenes App Service-felügyelt tanúsítvány](#create-a-free-certificate-preview) vagy az App [Service-tanúsítvány](#import-an-app-service-certificate) már megfelel az App Service követelményeinek. Ha úgy dönt, hogy feltölt vagy importál egy privát tanúsítványt az App Service szolgáltatásba, a tanúsítványnak meg kell felelnie az alábbi követelményeknek:

* Jelszóval [védett PFX-fájlként](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions) exportálva
* Legalább 2048 bit hosszúságú titkos kulcsot kell tartalmaznia.
* Tartalmaznia kell a tanúsítványláncban lévő összes köztes tanúsítványt.

Egyéni tartomány TLS-kötésben való védelméhez a tanúsítvány további követelményekkel rendelkezik:

* Kiterjesztett [kulcshasználatot](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) tartalmaz a kiszolgáló hitelesítéséhez (OID = 1.3.6.1.5.7.3.1)
* A tanúsítványt megbízható hitelesítésszolgáltatónak kell aláírnia.

> [!NOTE]
> **Az elliptikus görbéjű titkosítási (ECC-) tanúsítványok** együttműködnek az App Service-szel, ez a cikk azonban erre nem tér ki. A hitelesítésszolgáltatóval együttműködve dolgozzák ki az ECC-tanúsítványok létrehozására szolgáló lépéseket.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Ingyenes tanúsítvány létrehozása (előzetes verzió)

Az ingyenes App Service felügyelt tanúsítvány egy kulcsrakész megoldás az egyéni DNS-név védelmére az App Service-ben. Ez egy teljesen működőképes TLS/SSL tanúsítvány, amelyet az App Service kezel és automatikusan megújít. Az ingyenes tanúsítvány a következő korlátozásokkal jár:

- Nem támogatja a helyettesítő karakteres tanúsítványokat.
- Nem támogatja a meztelen domaineket.
- Nem exportálható.
- Nem támogatja a DNS A-rekordokat.

> [!NOTE]
> Az ingyenes tanúsítványt a DigiCert bocsátotta ki. Egyes legfelső szintű tartományok esetében explicit módon engedélyeznie kell a DigiCert tanúsítványkibocsátóként a következő `0 issue digicert.com`értékű [CAA tartományrekordot.](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization)
> 

Ingyenes App Service felügyelt tanúsítvány létrehozása:

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>válassza a bal oldali **menüben** > az App Services**\<alkalmazásnév>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** > **titkoskulcs-tanúsítványainak (.pfx)** > **Alkalmazásszolgáltatás által felügyelt tanúsítvány létrehozása**lehetőséget.

![Ingyenes tanúsítvány létrehozása az App Service szolgáltatásban](./media/configure-ssl-certificate/create-free-cert.png)

A párbeszédpanelen megjelenik minden olyan nem meztelen tartomány, amely megfelelően van leképezve az alkalmazásra CNAME rekorddal. Jelölje ki azt az egyéni tartományt, amelyhez ingyenes tanúsítványt szeretne létrehozni, és válassza a **Létrehozás gombot.** Minden támogatott egyéni tartományhoz csak egy tanúsítványt hozhat létre.

Amikor a művelet befejeződik, megjelenik a tanúsítvány a **személyes kulcs tanúsítványok** listájában.

![Ingyenes tanúsítvány létrehozása kész](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Ahhoz, hogy ezzel a tanúsítvánnyal egyéni tartományt biztosítson, még létre kell hoznia egy tanúsítványkötést. Kövesse a [Kötés létrehozása](configure-ssl-bindings.md#create-binding)című részben leírt lépéseket.
>

## <a name="import-an-app-service-certificate"></a>App-szolgáltatástanúsítvány importálása

Ha az Azure-ból vásárol egy App Service-tanúsítványt, az Azure a következő feladatokat kezeli:

- Gondoskodik a GoDaddy vásárlási folyamatáról.
- A tanúsítvány tartományellenőrzését hajtja végre.
- Fenntartja a tanúsítványt az [Azure Key Vaultban.](../key-vault/key-vault-overview.md)
- Kezeli a tanúsítványmegújítást (lásd: [Tanúsítvány megújítása](#renew-certificate)).
- A tanúsítvány automatikus szinkronizálása az App Service-alkalmazások importált példányaival.

App Service-tanúsítvány megvásárlásához nyissa meg a [Tanúsítványrendelés indítása lehetőséget.](#start-certificate-order)

Ha már rendelkezik működő App Service-tanúsítvánnyal, a következőket teheti:

- [Importálja a tanúsítványt az App Service szolgáltatásba.](#import-certificate-into-app-service)
- [Kezelje a tanúsítványt,](#manage-app-service-certificates)például megújítsa, újrakulcsolja és exportálja.

### <a name="start-certificate-order"></a>Tanúsítványrendelés indítása

Indítsa el az App Service-tanúsítványrendelést az <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service-tanúsítvány létrehozása lapon.</a>

![Az App Service-tanúsítvány vásárlásának megkezdése](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Az alábbi táblázat segítségével konfigurálhatja a tanúsítványt. Ha végzett, kattintson a **Létrehozás** gombra.

| Beállítás | Leírás |
|-|-|
| Név | Az App Service-tanúsítvány rövid neve. |
| Meztelen domain állomásnév | Itt adhatja meg a gyökértartományt. A kiállított tanúsítvány *biztosítja mind* a `www` gyökértartományt, mind az altartományt. A kiállított tanúsítványban a Köznapi név mező tartalmazza a gyökértartományt, a Tulajdonos alternatív neve mező pedig a `www` tartományt. Ha csak az altartományokat szeretné biztonságossá tenni, itt adja `mysubdomain.contoso.com`meg az altartomány teljesen minősített tartománynevét (például ).|
| Előfizetés | A tanúsítványt tartalmazó előfizetés. |
| Erőforráscsoport | A tanúsítványt tartalmazó erőforráscsoport. Használhat például egy új erőforráscsoportot, vagy kiválaszthatja ugyanazt az erőforráscsoportot, mint az App Service-alkalmazás. |
| Tanúsítvány termékváltozata | Meghatározza a létrehozandó tanúsítvány típusát, legyen az szabványos vagy [helyettesítő tanúsítvány.](https://wikipedia.org/wiki/Wildcard_certificate) |
| Jogi feltételek | Kattintson ide annak megerősítéséhez, hogy egyetért a jogi feltételekkel. A tanúsítványokat a GoDaddy szerezte be. |

### <a name="store-in-azure-key-vault"></a>Áruház az Azure Key Vaultban

A tanúsítványvásárlási folyamat befejezése után még néhány lépést el kell végeznie a tanúsítvány használatának megkezdése előtt. 

Jelölje ki a tanúsítványt az [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd kattintson a **Tanúsítvány konfigurációs** > **lépés 1: Áruház parancsra.**

![Az App Service-tanúsítvány Key Vault-tárolójának konfigurálása](./media/configure-ssl-certificate/configure-key-vault.png)

[A Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) egy Azure-szolgáltatás, amely segít megvédeni a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkos kulcsokat. Az App Service-tanúsítványok tárolása.

A **Key Vault állapota** lapon kattintson a **Key Vault repository** egy új tárolót, vagy válasszon egy meglévő trezort. Ha úgy dönt, hogy hozzon létre egy új tárolót, használja az alábbi táblázatot, hogy segítsen beállítani a tárolót, és kattintson a Create. Hozza létre az új Key Vault belül ugyanabban az előfizetésben és erőforráscsoport, mint az App Service-alkalmazás.

| Beállítás | Leírás |
|-|-|
| Név | Egyedi név, amely alfanumerikus karaktereket és kötőjeleket tartalmaz. |
| Erőforráscsoport | Javaslatként válassza ki ugyanazt az erőforráscsoportot, mint az App Service-tanúsítvány. |
| Hely | Válassza ki ugyanazt a helyet, mint az App Service-alkalmazás. |
| Tarifacsomag | További információt az [Azure Key Vault díjszabási részletei című témakörben talál.](https://azure.microsoft.com/pricing/details/key-vault/) |
| Hozzáférési szabályzatok| Meghatározza az alkalmazásokat és a tároló erőforrásaihoz való szabad hozzáférést. Beállíthatja később, a következő lépéseket [a Grant több alkalmazás hozzáférést a key vault.](../key-vault/key-vault-group-permissions-for-apps.md) |
| Virtuális hálózati hozzáférés | Korlátozza a tároló hozzáférést bizonyos Azure virtuális hálózatokhoz. Később konfigurálhatja, az Azure [Key Vault tűzfalak és virtuális hálózatok konfigurálása](../key-vault/key-vault-network-security.md) című lépés lépéseit követve. |

Miután kiválasztotta a tárolót, zárja be a **Key Vault repository** lapot. **1. lépés: Az áruház** beállításnak zöld pipát kell megjelenítenie a siker érdekében. Tartsa nyitva az oldalt a következő lépéshez.

### <a name="verify-domain-ownership"></a>Tartomány tulajdonjogának ellenőrzése

Az utolsó lépésben használt **tanúsítványkonfigurációs** lapon kattintson a **2.**

![Az App Service-tanúsítvány tartományának ellenőrzése](./media/configure-ssl-certificate/verify-domain.png)

Válassza **az App Service-ellenőrzés lehetőséget.** Mivel már leképezte a tartományt a webalkalmazáshoz [(lásd: Előfeltételek),](#prerequisites)már ellenőriztük. Csak kattintson **az Ellenőrzés gombra** a lépés befejezéséhez. Kattintson a **Frissítés** gombra, amíg a **Tanúsítvány tartományellenőrzött nem** lesz.

> [!NOTE]
> A tartományellenőrzési módszerek négy típusa támogatott: 
> 
> - **App Service** - A legkényelmesebb lehetőség, ha a tartomány már le van képezve egy App Service-alkalmazás ugyanabban az előfizetésben. Kihasználja azt a tényt, hogy az App Service-alkalmazás már ellenőrizte a tartomány tulajdonjogát.
> - **Tartomány** – Az [Azure-ból vásárolt App Service-tartomány](manage-custom-dns-buy-domain.md)ellenőrzése. Az Azure automatikusan hozzáadja az ellenőrző TXT rekordot, és befejezi a folyamatot.
> - **Mail** – A tartomány ellenőrzése e-mail küldésével a tartományi rendszergazdának. A beállítás kiválasztásakor a használati utasítás t adunk meg.
> - **Kézikönyv** – A tartomány ellenőrzése HTML-lap **(csak szabványos** tanúsítvány esetén) vagy DNS TXT rekord használatával. A beállítás kiválasztásakor a használati utasítás t adunk meg.

### <a name="import-certificate-into-app-service"></a>Tanúsítvány importálása az App Service szolgáltatásba

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>válassza a bal oldali **menüben** > az App Services**\<alkalmazásnév>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** > **titkoskulcs-tanúsítványainak (.pfx)** > **Importálása appszolgáltatás-tanúsítványt.**

![App Service-tanúsítvány importálása az App Service-ben](./media/configure-ssl-certificate/import-app-service-cert.png)

Válassza ki az imént vásárolt tanúsítványt, és válassza az **OK gombot.**

Amikor a művelet befejeződik, megjelenik a tanúsítvány a **személyes kulcs tanúsítványok** listájában.

![Az App Service-tanúsítvány importálása befejeződött](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Ahhoz, hogy ezzel a tanúsítvánnyal egyéni tartományt biztosítson, még létre kell hoznia egy tanúsítványkötést. Kövesse a [Kötés létrehozása](configure-ssl-bindings.md#create-binding)című részben leírt lépéseket.
>

## <a name="import-a-certificate-from-key-vault"></a>Tanúsítvány importálása a Key Vaultból

Ha az Azure Key Vault használatával kezeli a tanúsítványokat, importálhat egy PKCS12 tanúsítványt a Key Vaultból az App Service-be, [amennyiben az megfelel a követelményeknek.](#private-certificate-requirements)

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>válassza a bal oldali **menüben** > az App Services**\<alkalmazásnév>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** > **titkoskulcs-tanúsítványainak (.pfx)** > **importálása key vault tanúsítványát.**

![Key Vault-tanúsítvány importálása az App Service-ben](./media/configure-ssl-certificate/import-key-vault-cert.png)

Az alábbi táblázat segítségével kiválaszthatja a tanúsítványt.

| Beállítás | Leírás |
|-|-|
| Előfizetés | Az az előfizetés, amelyhez a Key Vault tartozik. |
| Key Vault | Az importálni kívánt tanúsítvánnyal rendelkező tároló. |
| Tanúsítvány | Válasszon a tárolóban található PKCS12 tanúsítványok listájából. A tárolóban lévő összes PKCS12 tanúsítvány megjelenik az ujjlenyomataikkal, de nem mindegyik támogatott az App Service-ben. |

Amikor a művelet befejeződik, megjelenik a tanúsítvány a **személyes kulcs tanúsítványok** listájában. Ha az importálás hiba miatt sikertelen, a tanúsítvány nem felel meg az [App Service követelményeinek.](#private-certificate-requirements)

![A Key Vault-tanúsítvány importálása befejeződött](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Ahhoz, hogy ezzel a tanúsítvánnyal egyéni tartományt biztosítson, még létre kell hoznia egy tanúsítványkötést. Kövesse a [Kötés létrehozása](configure-ssl-bindings.md#create-binding)című részben leírt lépéseket.
>

## <a name="upload-a-private-certificate"></a>Privát tanúsítvány feltöltése

Miután beszerzett egy tanúsítványt a tanúsítványszolgáltatótól, kövesse az ebben a szakaszban leírt lépéseket, hogy készen álljon az App Service szolgáltatásra.

### <a name="merge-intermediate-certificates"></a>Köztes tanúsítványok egyesítése

Ha a hitelesítésszolgáltató több tanúsítványt biztosít a tanúsítványláncban, sorrendben kell egyesítenie a tanúsítványokat.

Ehhez nyissa meg a kapott tanúsítványokat egy szövegszerkesztőben.

Hozzon létre egy _mergedcertificate.crt_ nevű fájlt az egyesített tanúsítvány számára. Egy szövegszerkesztőben másolja ebbe a fájlba az egyes tanúsítványok tartalmát. A tanúsítványok sorrendjének egyeznie kell a tanúsítványláncban lévő sorrenddel, a saját tanúsítvánnyal kezdve és a főtanúsítvánnyal végződve. Az alábbi példához hasonlóan néz ki:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Tanúsítvány exportálása PFX-fájlba

Exportálja az egyesített TLS/SSL-tanúsítványt azzal a személyes kulccsal, amelyhez a tanúsítványkérelmet létrehozták.

Ha OpenSSL használatával hozta létre a tanúsítványkérést, akkor létrehozott egy titkoskulcsfájlt. A tanúsítvány PFX-fájlba exportáláshoz futtassa az alábbi parancsot. Cserélje le a helyőrzők _ &lt;titkoskulcs-fájl>_ és _ &lt;az egyesített tanúsítványfájl->_ a személyes kulcs elérési útjaira és az egyesített tanúsítványfájlra.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Amikor a rendszer megkéri, adjon meg egy exportálási jelszót. Ezt a jelszót fogja használni, amikor később feltölti a TLS/SSL tanúsítványát az App Service szolgáltatásba.

Ha az IIS vagy a _Certreq.exe_ használatával hozta létre a tanúsítványkérést, telepítse a tanúsítványt a helyi számítógépre, majd [exportálja a tanúsítványt PFX-fájlba](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-certificate-to-app-service"></a>Tanúsítvány feltöltése az App Service szolgáltatásba

Most már készen áll a tanúsítvány feltöltésére az App Service-be.

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>válassza a bal oldali **menüben** > az App Services**\<alkalmazásnév>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján válassza a **TLS/SSL-beállítások** > **személyes kulcstanúsítványainak (.pfx)** > **feltöltési tanúsítványát.**

![Privát tanúsítvány feltöltése az App Service szolgáltatásban](./media/configure-ssl-certificate/upload-private-cert.png)

A **PFX Certificate File** (PFX-tanúsítványfájl) mezőben válassza ki a PFX-fájlt. A **Certificate password** (Tanúsítvány jelszava) területen írja be a PFX-fájl exportálásakor létrehozott jelszót. Ha végzett, kattintson **a Feltöltés gombra.** 

Amikor a művelet befejeződik, megjelenik a tanúsítvány a **személyes kulcs tanúsítványok** listájában.

![A tanúsítvány feltöltése befejeződött](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Ahhoz, hogy ezzel a tanúsítvánnyal egyéni tartományt biztosítson, még létre kell hoznia egy tanúsítványkötést. Kövesse a [Kötés létrehozása](configure-ssl-bindings.md#create-binding)című részben leírt lépéseket.
>

## <a name="upload-a-public-certificate"></a>Nyilvános tanúsítvány feltöltése

A nyilvános *tanúsítványokat .cer* formátumban támogatják. 

Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>válassza a bal oldali **menüben** > az App Services**\<alkalmazásnév>** lehetőséget.

Az alkalmazás bal oldali navigációs sávján kattintson a **TLS/SSL-beállítások** > **nyilvános tanúsítványok (.cer)** > Nyilvános**kulcstanúsítvány feltöltése elemre.**

A **Név mezőbe**írja be a tanúsítvány nevét. A **CER tanúsítványfájlban**jelölje ki a CER-fájlt.

Kattintson a **Feltöltés** gombra.

![Nyilvános tanúsítvány feltöltése az App Service szolgáltatásban](./media/configure-ssl-certificate/upload-public-cert.png)

A tanúsítvány feltöltése után másolja a tanúsítvány ujjlenyomatát, és olvassa el [A tanúsítvány elérhetővé tétele](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)című témakört.

## <a name="manage-app-service-certificates"></a>App Service-tanúsítványok kezelése

Ez a szakasz bemutatja, hogyan kezelheti az App Service-tanúsítvány importálása szolgáltatásában vásárolt App [Service-tanúsítványt.](#import-an-app-service-certificate)

- [Újrakulcstanúsítvány](#rekey-certificate)
- [Tanúsítvány megújítása](#renew-certificate)
- [Tanúsítvány exportálása](#export-certificate)
- [Tanúsítvány törlése](#delete-certificate)

### <a name="rekey-certificate"></a>Újrakulcstanúsítvány

Ha úgy gondolja, hogy a tanúsítvány személyes kulcsa biztonsága sérül, újrakulcsolhatja a tanúsítványt. Jelölje ki a tanúsítványt az [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd válassza a **Rekey and Sync** lehetőséget a bal oldali navigációs sávon.

A folyamat elindításához kattintson a **Rekey** gombra. Ez a folyamat 1-10 percet is igénybe vehet.

![App Service-tanúsítvány újrakulcsa](./media/configure-ssl-certificate/rekey-app-service-cert.png)

A tanúsítvány újrakulcsolása a tanúsítványt a hitelesítésszolgáltatótól kiállított új tanúsítvánnyal állítja össze.

Az újrakulcsos művelet befejezése után kattintson a **Szinkronizálás gombra.** A szinkronizálási művelet automatikusan frissíti a tanúsítvány állomásnév-kötéseit az App Service-ben anélkül, hogy az alkalmazásoknak állásidőt okozna.

> [!NOTE]
> Ha nem kattint a **Szinkronizálás gombra,** az App Service 48 órán belül automatikusan szinkronizálja a tanúsítványt.

### <a name="renew-certificate"></a>Tanúsítvány megújítása

Ha bármikor be szeretné kapcsolni a tanúsítvány automatikus megújítását, jelölje ki a tanúsítványt az [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd kattintson a bal oldali navigációs sáv **automatikus megújítási beállításai hivatkozási** elemére. Alapértelmezés szerint az App Service-tanúsítványok egyéves érvényességi idővel rendelkeznek.

Válassza **a Be** lehetőséget, és kattintson a Mentés **gombra.** A tanúsítványok automatikusan megújulhatnak 60 nappal a lejárat előtt, ha be van kapcsolva az automatikus megújítás.

![Az App Service-tanúsítvány automatikus megújítása](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

A tanúsítvány manuális megújításához kattintson a **Kézi megújítás gombra.** Kérheti a tanúsítvány manuális megújítását 60 nappal a lejárat előtt.

A megújítási művelet befejezése után kattintson a **Szinkronizálás gombra.** A szinkronizálási művelet automatikusan frissíti a tanúsítvány állomásnév-kötéseit az App Service-ben anélkül, hogy az alkalmazásoknak állásidőt okozna.

> [!NOTE]
> Ha nem kattint a **Szinkronizálás gombra,** az App Service 48 órán belül automatikusan szinkronizálja a tanúsítványt.

### <a name="export-certificate"></a>Tanúsítvány exportálása

Mivel az App Service-tanúsítvány [egy Key Vault titkos kulcs,](../key-vault/about-keys-secrets-and-certificates.md#key-vault-secrets)exportálhatja a PFX-példányt, és más Azure-szolgáltatásokhoz vagy az Azure-on kívül is használhatja.

Ha az App Service-tanúsítványt PFX-fájlként szeretné exportálni, futtassa a következő parancsokat a [Cloud Shell](https://shell.azure.com)ben. Helyileg is futtathatja, ha [telepítette az Azure CLI-t.](https://docs.microsoft.com/cli/azure/install-azure-cli) Cserélje le a helyőrzőket az [App Service-tanúsítvány létrehozásakor használt nevekre.](#start-certificate-order)

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

A letöltött *appservicecertificate.pfx* fájl egy nyers PKCS12 fájl, amely nyilvános és privát tanúsítványokat is tartalmaz. Minden kérdésben használjon üres karakterláncot az importálási jelszóhoz és a PEM-jelszóhoz.

### <a name="delete-certificate"></a>Tanúsítvány törlése 

Az App Service-tanúsítvány törlése végleges és visszafordíthatatlan. Az App Service-tanúsítvány erőforrásának törlése a tanúsítvány visszavonását eredményezi. Az App Service-ben ezzel a tanúsítvánnyal rendelkező kötések érvénytelenné válnak. A véletlen törlés megelőzése érdekében az Azure zárolja a tanúsítványt. Az App Service-tanúsítvány törléséhez először el kell távolítania a tanúsítvány törlési zárolását.

Jelölje ki a tanúsítványt az [App Service-tanúsítványok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) lapon, majd válassza a **Zárolások** elemet a bal oldali navigációs sávon.

Keresse meg a tanúsítvány zárolását **törlés**típusú zárolással. A tőle jobbra válassza a **Törlés**lehetőséget.

![Zárolás törlése az App Service-tanúsítványhoz](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Most törölheti az App Service-tanúsítványt. A bal oldali navigációs sávon válassza az **Áttekintés** > **törlése lehetőséget.** A megerősítést kérő párbeszédpanelen írja be a tanúsítvány nevét, és válassza az **OK gombot.**

## <a name="automate-with-scripts"></a>Automatizálás szkriptekkel

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>További erőforrások

* [Egyéni DNS-név biztonságossá tétele TLS/SSL-kötéssel az Azure App Service-ben](configure-ssl-bindings.md)
* [HTTPS kényszerítése](configure-ssl-bindings.md#enforce-https)
* [A TLS 1.1/1.2 kényszerítése](configure-ssl-bindings.md#enforce-tls-versions)
* [TLS-/SSL-tanúsítvány használata a kódban az Azure App Service-ben](configure-ssl-certificate-in-code.md)
* [GYAKORI KÉRDÉSEK : App Service-tanúsítványok](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
