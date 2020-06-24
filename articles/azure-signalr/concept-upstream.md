---
title: Felsőbb rétegbeli beállítások az Azure Signaler szolgáltatásban
description: A felsőbb rétegbeli üzenetek felső beállításainak és protokolljának bemutatása
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 7434e8796ddcd89968a0ffa0328a823d635f51c9
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988665"
---
# <a name="upstream-settings"></a>Felsőbb rétegbeli beállítások

A felsőbb rétegbeli funkció lehetővé teszi, hogy a Signaler szolgáltatás üzeneteket és kapcsolódási eseményeket küldjön a végpontok számára kiszolgáló nélküli módban. A felsőbb réteg használatával kiszolgáló nélküli módban hívható meg a hub metódusa az ügyfelektől, és engedélyezheti a végpontok számára, hogy értesítést kapjanak, amikor az ügyfélkapcsolatok csatlakoztatva vannak vagy le vannak választva.

> [!NOTE]
> Csak a kiszolgáló nélküli mód konfigurálhatja a felsőbb rétegbeli beállításokat.

## <a name="upstream-settings-details"></a>Felsőbb rétegbeli beállítások részletei

A felsőbb rétegbeli beállítások a rendelés bizalmas elemeinek listájából állnak. Mindegyik elem egy-ból áll `URL Template` , amely megadja, hogy az üzenetek hová legyenek elküldve, egy készletbe `Rules` és egy `Authentication` konfigurációba. Ha a megadott esemény bekövetkezik, a rendszer egy-egy sorrendben ellenőrzi az elemeket, `Rules` és az üzeneteket a rendszer az első megfelelő elem felsőbb rétegbeli URL-címére küldi.

### <a name="url-template-settings"></a>URL-sablon beállításai

Az URL-cím a különböző mintázatok támogatásához paraméterekkel is rendelkezhet. Három előre definiált paraméter létezik:

|Előre definiált paraméter|Leírás|
|---------|---------|
|hub| A hub a jelző fogalma. A hub elkülönítési egység, a felhasználók és az üzenetek kézbesítésének hatóköre egy hubhoz van korlátozva.|
|Kategória| A kategória a következő értékek egyike lehet: <ul><li>**kapcsolatok**: kapcsolat élettartamának eseményei. Akkor aktiválódik, amikor egy ügyfélkapcsolat csatlakoztatva van vagy le van választva. A *csatlakoztatott* és a *leválasztott* eseményeket is beleértve</li><li>**üzenetek**: akkor aktiválódik, amikor az ügyfelek meghívja a hub metódust. Beleértve az összes többi eseményt, kivéve a *kapcsolatok* kategóriájában lévőket</li></ul>|
|esemény| Az *üzenetek* kategória esetében az esemény az a *cél* , amely az ügyfelek által küldött [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) szerepel. A *kapcsolatok* kategória esetében csak a *csatlakoztatott* és a *leválasztott* kapcsolat van használatban.|

Ezek az előre definiált paraméterek használhatók a `URL Pattern` és a paraméterekben a felsőbb rétegbeli URL-cím kiértékelése során megadott értékkel. Például 
```
http://host.com/{hub}/api/{category}/{event}
```
Ha a "chat" hub-kapcsolat csatlakoztatva van, a rendszer üzenetet küld az URL-címre:
```
http://host.com/chat/api/connections/connected
```
Ha a hub egyik ügyfele `chat` meghívja a hub metódust `broadcast` , a rendszer üzenetet küld az URL-címre:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rules-settings"></a>Szabályok beállításai

A *központi szabályokra*, a *kategóriákra* és az *események szabályaira* vonatkozó szabályokat külön is megadhat. Az egyeztetési szabály három formátumot támogat. Példaként tekintse meg az *események szabályait* :
- Használja a csillag (*) karaktert az események egyeztetéséhez.
- A felhasználó vesszővel (,) csatlakozhat több eseményhez. Például megfelel a `connected, disconnected` *csatlakoztatott* és a *leválasztott*eseményeknek.
- A teljes esemény nevét használja az eseménynek megfelelően. Például megfelel a `connected` *csatlakoztatott* eseménynek.

### <a name="authentication-settings"></a>Hitelesítési beállítások

Az egyes felsőbb rétegbeli beállítási elemek *hitelesítését* külön is konfigurálhatja. A konfigurált *hitelesítéskor*a rendszer egy tokent állít be a felsőbb rétegbeli üzenet *hitelesítési* fejlécében. Jelenlegi, a Signaler szolgáltatás a következő hitelesítési típust támogatja
- Nincs
- ManagedIdentity

A *ManagedIdentity*kiválasztása után előre engedélyeznie kell a felügyelt identitást a Signaler szolgáltatásban, és opcionálisan meg kell adnia egy *erőforrást*. További információ: a [felügyelt identitások használata az Azure signaler szolgáltatáshoz](howto-use-managed-identity.md) .

## <a name="create-upstream-settings"></a>Felsőbb rétegbeli beállítások létrehozása

### <a name="create-upstream-settings-via-azure-portal"></a>Felsőbb rétegbeli beállítások létrehozása Azure Portal használatával

1. Nyissa meg az Azure Signaler szolgáltatást.
2. Kattintson a nyájas *beállításokra* , és váltson a *szolgáltatás módba* a *kiszolgáló*nélküli értékre. A *felsőbb rétegbeli beállítások* az alábbiak szerint jelennek meg:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Felsőbb rétegbeli beállítások":::

3. Töltse ki az URL-címeket a *felsőbb rétegbeli URL-mintába*, majd a beállítások, például a *hub-szabályok* az alapértelmezett értéket fogják megjeleníteni.
4. Az olyan beállítások megadásához, mint például a *hub-szabályok*, az *események szabályai*, a *Kategória szabályai* és a *felsőbb rétegbeli hitelesítés*, kattintson a *hub-szabályok*értékére. Olyan oldal, amely lehetővé teszi a beállítások szerkesztését, ahogy az alábbi ábrán látható:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Felsőbb rétegbeli beállítások":::

5. A *felsőbb rétegbeli hitelesítés*beállításához először engedélyezze a felügyelt identitást, majd válassza a *felügyelt identitás használata* a *felsőbb rétegbeli hitelesítés*alatt lehetőséget. Az igényeknek megfelelően az *Auth erőforrás-azonosító*területen bármelyik lehetőség közül választhat. A részletekért lásd: [a felügyelt identitás engedélyezése](howto-use-managed-identity.md) .

### <a name="create-upstream-settings-via-arm-template"></a>Felsőbb rétegbeli beállítások létrehozása ARM-sablon használatával

A felsőbb rétegbeli beállítások [Resource Manager-sablonnal](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)történő létrehozásához állítsa be a tulajdonságot `upstream` a `properties` tulajdonságban. Az alábbi kódrészletek bemutatják, hogyan állíthatja be a `upstream` tulajdonságot a felsőbb rétegbeli beállítások létrehozásához és frissítéséhez.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="signalr-serverless-protocol"></a>A jelző kiszolgáló nélküli protokollja

A signaler szolgáltatás üzeneteket küld a következő protokollokat követő végpontoknak.

### <a name="method"></a>Metódus

POST

### <a name="request-header"></a>Kérelem fejléce

|Name |Leírás|
|---------|---------|
|X-ASRS-kapcsolatazonosító |Az Ügyfélkapcsolat azonosítója|
|X-ASRS – hub |Az a hub, amelyhez az Ügyfélkapcsolat tartozik|
|X-ASRS – kategória |Az a kategória, amelyhez az üzenet tartozik|
|X-ASRS-Event |Az az esemény, amelyhez az üzenet tartozik|
|X-ASRS – aláírás |Az érvényesítéshez használt HMAC. Részletekért lásd az [aláírást](#signature) .|
|X-ASRS-User-jogcím |Az Ügyfélkapcsolat jogcímeinek egy csoportja|
|X-ASRS-User-ID |Az üzenetet küldő ügyfél felhasználói identitása|
|X-ASRS – ügyfél – lekérdezés |A kérés lekérdezése, amikor az ügyfelek csatlakoznak a szolgáltatáshoz|
|Hitelesítés |Nem kötelező jogkivonat a *ManagedIdentity* használatakor |

### <a name="request-body"></a>A kérés törzse

#### <a name="connected"></a>Csatlakozva

Content-Type: Application/JSON

#### <a name="disconnected"></a>Leválasztott

Content-Type:`application/json`

|Name  |Típus  |Leírás  |
|---------|---------|---------|
|Hiba |sztring |A kapcsolatok lezárult hibaüzenete. Üres, ha a kapcsolatok hiba nélkül zárulnak|

#### <a name="invocation-message"></a>Meghívási üzenet

Content-Type: `application/json` vagy`application/x-msgpack`

|Name  |Típus  |Leírás  |
|---------|---------|---------|
|InvocationId |sztring | Egy választható sztring egy Meghívási üzenetet jelöl. Részletek keresése a [hívásokban](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Cél |sztring | Ugyanaz, mint a [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)megjelenő *esemény* és a *cél* . |
|Argumentumok |Objektum tömbje |A célhelyen hivatkozott metódusra alkalmazandó argumentumokat tartalmazó tömb. |

### <a name="signature"></a>Aláírás

A szolgáltatás a `X-ASRS-Connection-Id` kulcsként az elsődleges hozzáférési kulcs és a másodlagos elérési kulcs használatával kiszámítja az érték sha256 kódját `HMAC` , és beállítja azt a `X-ASRS-Signature` fejlécben, amikor http-kéréseket küld a felsőbb rétegnek:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások használata az Azure Signaler szolgáltatásban](howto-use-managed-identity.md)
- [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](signalr-concept-serverless-development-config.md)
