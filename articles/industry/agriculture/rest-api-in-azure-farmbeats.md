---
title: Referencia
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 717a09d8377a7b95fe24300cc65222f307e7419f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437529"
---
# <a name="references"></a>Referencia

Ez a cikk ismerteti az Azure FarmBeats API-k.

## <a name="rest-api"></a>REST API

Az Azure FarmBeats API-k szabványosított RESTful felületet biztosítanak a mezőgazdasági vállalkozások számára JSON-alapú válaszokkal, hogy kihasználhassa az Azure FarmBeats képességeit, például:

- API-k az érzékelő, kamera, drón, időjárás, műhold és válogatott földi adatok levételéhez.
- Az adatok normalizálása és környezetnyelvi összehangolása a közös adatszolgáltatók között.
- Sémaozott hozzáférési és lekérdezési képességek minden bevitt adaton.
- Lekérdezhető metaadatok automatikus létrehozása az agronómiai jellemzők alapján.
- Automatikusan generált idősorozat-összesítések a gyors modellépítéshez.
- Integrált Azure Data Factory motor, amely egyszerűen hozhat létre egyéni adatfeldolgozási folyamatokat.

## <a name="application-development"></a>Alkalmazásfejlesztés

A FarmBeats API-k swagger műszaki dokumentációt tartalmaznak. Az api-król és a hozzájuk tartozó kérésekről és válaszokról a [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)című témakörben talál további információt.

Az alábbi táblázat a FarmBeats Datahub összes objektumát és erőforrását foglalja össze:

| Objektumok és erőforrások | Leírás
--- | ---|
Farm | A farm a FarmBeats rendszeren belüli fizikai érdekes helynek felel meg. Minden gazdaságnak van egy farm neve és egy egyedi farmazonosítója. |
Eszköz  | Az eszköz a gazdaságban lévő fizikai eszköznek felel meg. Minden eszköz egyedi eszközazonosítóval rendelkezik. Az eszköz általában ki van építve egy farmazonosítóval rendelkező farmhoz.
DeviceModel  | A DeviceModel megfelel az eszköz metaadatainak, például a gyártónak és az eszköz típusának, amely átjáró vagy csomópont.
Érzékelő  | Az érzékelő egy fizikai érzékelőnek felel meg, amely rögzíti az értékeket. Az érzékelő általában eszközazonosítóval rendelkező eszközhöz csatlakozik.
Érzékelőmodell  | A SensorModel megfelel az érzékelő metaadatainak, például a gyártónak, az analóg vagy digitális érzékelő típusának, valamint az érzékelő mérésének, például a környezeti hőmérsékletnek és a nyomásnak.
Telemetria  | Telemetriai lehetővé teszi, hogy egy adott érzékelő és időtartomány telemetriai üzenetek olvasása.
Feladat  | A feladat a FarmBeats rendszerben végrehajtott tevékenységek minden munkafolyamatának felel meg a kívánt kimenet eléréséhez. Minden feladat egy feladatazonosítóhoz és feladattípushoz van társítva.
Feladattípusa  | A JobType a rendszer által támogatott különböző feladattípusoknak felel meg. A rendszer által definiált és a felhasználó által definiált feladattípusok szerepelnek.
Kiterjesztett típus  | Az ExtendedType a rendszerben és a felhasználó által definiált típusok listájának felel meg. Az ExtendedType segít új érzékelő, jelenet vagy jelenetfájltípus beállításában a FarmBeats rendszerben.
Partner  | A partner megfelel a FarmBeats érzékelő- és képintegrációs partnerének.
Jelenet  | A jelenet a farm környezetében létrehozott kimenetnek felel meg. Minden jelenethez tartozik egy jelenetazonosító, jelenetforrás, jelenettípus és farmazonosító. Minden jelenetazonosítóhoz több jelenetfájl is tartozhat.
Jelenetfájl |A SceneFile az egyetlen jelenethez létrehozott összes fájlnak felel meg. Egyetlen jelenetazonosítóhoz több SceneFile azonosító is tartozhat.
Szabály  |A szabály megfelel annak a feltételnek, hogy a farmokkal kapcsolatos adatok riasztást váltsanak ki. Minden szabály a farm adatainak kontextusában van.
Riasztás  | A riasztás egy értesítésnek felel meg, amely akkor jön létre, amikor egy szabályfeltétel teljesül. Minden riasztás egy szabály kontextusában van.
Szerepkördefiníció  | A RoleDefinition egy szerepkör engedélyezett és tiltott műveleteket határoz meg.
Szerepkör-hozzárendelés  |A Szerepkör-hozzárendelés egy szerepkör felhasználóhoz vagy egyszerű szolgáltatáshoz rendelésének felel meg.

### <a name="data-format"></a>Adatformátum

A JSON egy közös, nyelvfüggetlen adatformátum, amely egyszerű szövegábrázolást biztosít tetszőleges adatstruktúrákról. További információt a [JSON webhelyén talál.](https://www.json.org/)

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

A REST API-ra irányuló HTTP-kérelmeket az Azure Active Directory (Azure AD) védi.
Ha hitelesített kérelmet szeretne benyújtani a REST API-khoz, az ügyfélkód hoz hitelesítést érvényes hitelesítő adatokkal, mielőtt meghívhatna az API-t. A hitelesítést az Azure AD koordinálja a különböző szereplők között. Ez biztosítja az ügyfél egy hozzáférési jogkivonatot a hitelesítés igazolásaként. A jogkivonatezután elküldi a REST API-kérelmek HTTP-engedélyezési fejlécében. Ha többet szeretne megtudni az Azure AD-hitelesítésről, olvassa el az [Azure Active Directory](https://portal.azure.com) fejlesztőknek című témakört.

A hozzáférési jogkivonatot a következő API-kérelmekben kell elküldeni a fejlécszakaszban:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP-kérelem fejlécei

Az alábbiakban a leggyakoribb kérelemfejlécek, amelyeket meg kell adnia, amikor api-hívás az Azure FarmBeats Datahub.


**Fejléc** | **Leírás és példa**
--- | ---
Content-Type  | A kérelem formátuma (Content-Type: application/<format>). Az Azure FarmBeats Datahub API-k esetében a formátum JSON. Tartalom-típus: alkalmazás/json
Engedélyezés  | Megadja az API-híváshoz szükséges hozzáférési jogkivonatot. Engedélyezés: a tulajdonos <hozzáférési>
Elfogadás | A válasz formátuma. Az Azure FarmBeats Datahub API-k esetében a formátum JSON. Elfogadás: alkalmazás/json

### <a name="api-requests"></a>API-kérelmek

REST API-kérelem benyújtásához kombinálja a HTTP (GET, POST, PUT vagy DELETE) metódust, az API-szolgáltatás URL-címét, az URI-t egy erőforráshoz lekérdezve, adatok küldéséhez, frissítéshez vagy törléshez, majd egy vagy több HTTP-kérelemfejléc hozzáadását.

Az API-szolgáltatás URL-címe a Datahub URL-címe, például https://\<yourdatahub-website-name>.azurewebsites.net.

Szükség esetén lekérdezési paramétereket is megadhat a GET-hívásokhoz, korlátozhatja a válaszok ban lévő adatok méretét, és rendezheti az adatokat.

Az eszközök listájának lekéréséhez a következő mintakérelem áll a következő:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A legtöbb GET, POST és PUT hívások igényel JSON kérelem törzs.

A következő mintakérelem létrehoz egy eszközt. Ez a kérelem bemeneti JSON a kérelem törzse.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Lekérdezési paraméterek

REST GET-hívások esetén szűrheti, korlátozhatja az API-válasz ban lévő adatok méretét, és rendezheti az adatokat egy vagy több lekérdezési paramétert a kérelem URI-jára. A lekérdezési paramétereket az API dokumentációjában és az egyes GET-hívásokban találja.
Ha például lekérdezi az eszközök listáját (GET hívás /Device), a következő lekérdezési paraméterek adhatók meg:

![Eszközök listája](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Hibakezelés

Az Azure FarmBeats Datahub API-k a szabványos HTTP-hibákat adják vissza. A leggyakoribb hibakódok a következők:

 |Hibakód             | Leírás |
 |---                    | --- |
 |200                    | Sikeres |
 |201                    | Létrehozás (posta) sikeres |
 |400                    | Rossz kérés. Hiba történt a kérelemben. |
 |401                    | Jogosulatlan. Az API hívója nem jogosult az erőforrás elérésére. |
 |404                    | Az erőforrás nem található |
 |5XX KÖZÖTT                    | Belső kiszolgálóhiba. A hibakódok kezdve 5XX azt jelenti, hogy van néhány hiba a szerveren. További részleteket a kiszolgálónaplókban és a következő részben talál. |


A szabványos HTTP-hibák mellett az Azure FarmBeats Datahub API-k a következő formátumban is belső hibákat adnak vissza:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Ebben a példában egy farm létrehozásakor a kötelező "Név" mező nem volt megadva a bemeneti hasznos adatban. Az eredményül kapott hibaüzenet a következő:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Felhasználók vagy alkalmazásregisztrációk hozzáadása az Azure Active Directoryhoz

Az Azure FarmBeats API-k at egy felhasználó vagy egy alkalmazás regisztrációja az Azure Active Directoryban érhető el. Ha alkalmazást szeretne létrehozni az Azure Active Directoryban, kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és válassza az **Azure Active Directory** > **alkalmazás regisztrációja** > **Új regisztráció**lehetőséget. Másik lehetőségként használhat egy meglévő fiókot is.
2. Új fiók esetén tegye a következőket:

    - Írjon be egy nevet.
    - Válassza **a Fiókok lehetőséget ebben a szervezeti címtárban (egyetlen bérlő)**.
    - Használja a többi mező alapértelmezett értékeit.
    - Kattintson a **Register** (Regisztrálás) elemre.

3. Az új és meglévő alkalmazásregisztráció **áttekintése** ablaktáblán tegye a következőket:

    - Rögzítse az **ügyfélazonosítót** és **a bérlői azonosítót.**
    - Lépjen a **Tanúsítványok és titkok** elemre egy új ügyféltitok létrehozásához és az **ügyféltitok**rögzítéséhez.
    - Lépjen vissza az **Áttekintés**lapra, és válassza az Alkalmazás kezelése elemet a **helyi címtárban**.
    - Az **objektumazonosító**rögzítéséhez lépjen a **Tulajdonságok** elemre.

4. Nyissa meg a Datahub Swagger (https://<yourdatahub>.azurewebsites.net/swagger/index.html) webhelyet, és tegye a következőket:
    - Nyissa meg a **RoleAssignment API-t.**
    - Post végrehajtása az imént létrehozott **objektumazonosítóhoz** való **RoleAssignment-objektum** létrehozásához.

  > [!NOTE]
  > A felhasználók hozzáadásáról és az Active Directory regisztrációjáról az [Azure Active Directory című témakörben](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)talál további információt.

Az előző lépések befejezése után az alkalmazás regisztrációja (ügyfél) hívhatja az Azure FarmBeats API-k at egy hozzáférési jogkivonat használatával a tulajdonosi hitelesítés használatával.

A hozzáférési jogkivonat segítségével küldje el a következő API-kérelmek a fejléc szakaszban, mint:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
