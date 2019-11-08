---
title: FarmBeats-referenciák
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8e8e4524034f0a296045691309b065f8547bdaa0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797698"
---
# <a name="references"></a>Referencia

Az alábbiakban az Azure FarmBeats API-kat vázoló megjegyzések és utasítások gyűjteménye látható.

## <a name="rest-api"></a>REST API

Az Azure FarmBeats API-k egy szabványosított REST-felületet biztosító, JSON-alapú válaszokat biztosítanak a mezőgazdaság számára, és ez segít az Azure FarmBeats-képességek kihasználása során:

- Az API-k az érzékelő, a kamera, a drone, az időjárás, a szatellit és a kurátori alapok beszerzéséhez szükségesek.
- A Common adatszolgáltatókon keresztüli adatnormalizálás/contextualization.
- Sematikus hozzáférés és lekérdezési képességek az összes betöltött adattal kapcsolatban.
- A lekérdezhető metaadatok automatikus generálása agronómiai-funkciók alapján.  
- Automatikusan generált idősorozat-összesítések a gyors modell-létrehozáshoz.
- Az integrált Azure Data Factory (ADF) motor segítségével egyszerűen hozhat létre egyéni adatfeldolgozási folyamatokat.

## <a name="application-development"></a>Alkalmazásfejlesztés

Az API-k a hencegés műszaki dokumentációját tartalmazzák. [Tekintse meg az API](https://aka.ms/FarmBeatsDatahubSwagger) -k és a hozzájuk tartozó kérések/válaszok információit.

Ez a FarmBeats-adatközpontban lévő összes objektum/erőforrás összegzése:

Farm | A farm a FarmBeats rendszeren belüli fizikai helynek felel meg. Minden Farm rendelkezik egy farm nevével és egy egyedi Farm-AZONOSÍTÓval.
--- | ---|
Eszköz  | Az eszköz megfelel a farmban lévő fizikai eszköznek. Minden eszköz egyedi AZONOSÍTÓval rendelkezik. Az eszköz általában Farm-AZONOSÍTÓval rendelkező farmhoz van kiépítve.
deviceModel  | A DeviceModel megfelel az eszköz meta-adatának, például a gyártónak, az eszköz típusának vagy átjárónak vagy csomópontnak.
Érzékelő  | Az érzékelő olyan fizikai érzékelőnek felel meg, amely értékeket rögzít. Az érzékelő általában eszköz-AZONOSÍTÓval van csatlakoztatva egy eszközhöz.
SensorModel  | A SensorModel megfelel az érzékelő meta-adattípusának, például a gyártónak, az érzékelő típusának az analóg vagy a digitális, az érzékelő mértékének (például a környezeti hőmérséklet, a nyomás stb.).
Telemetria  | A telemetria lehetővé teszi egy adott érzékelő és időtartomány telemetria-üzeneteinek olvasását.
Feladat  | A feladat megfelel a tevékenységek munkafolyamatainak, amelyeket a rendszer a FarmBeats rendszeren hajt végre a kívánt kimenet beszerzéséhez. Mindegyik feladattípus egy adott AZONOSÍTÓJÚ és feladattípushoz van társítva.
JobType  | A JobType a rendszer által támogatott különböző feladatoknak felel meg. Ez magában foglalja a rendszer által definiált & felhasználó által definiált feladattípusokat.
ExtendedType  | A ExtendedType megfelel a rendszer & felhasználó által definiált típusai listájának. Ezzel a beállítással új érzékelőt vagy jelenetet vagy Scenefile-típust hozhat a FarmBeats rendszerbe.
Partner  | A partner megfelel a FarmBeats érzékelő-és képintegrációs partnerének
Jelenet  | A jelenet a farm környezetében generált kimenetnek felel meg. Minden jelenet rendelkezik egy jelenet-AZONOSÍTÓval, egy jelenet forrásával, a jelenet típusával és a hozzá társított Farm-AZONOSÍTÓval. Minden egyes jelenet-AZONOSÍTÓhoz több jelenet is tartozhat.
SceneFile |A SceneFile az összes fájlnak felel meg, amelyek egyetlen jelenethez jönnek létre. Egyetlen jelenet-AZONOSÍTÓhoz több SceneFile-azonosító is tartozhat.
Szabály  |A szabály megfelel a farmhoz kapcsolódó, riasztást kiváltó adat feltételének. Mindegyik szabály a farm adattartalmának kontextusában fog megjelenni.
Riasztás  | A riasztás megfelel egy értesítésnek, amely akkor jön létre, amikor egy szabály feltétele teljesül. Minden riasztás egy szabály kontextusában fog megjelenni.
Definíciós  | A definíciós az engedélyezett és a nem engedélyezett műveleteket definiálja egy szerepkörhöz.
RoleAssignment  |A RoleAssignment megfelel a szerepkör felhasználóhoz vagy egyszerű szolgáltatáshoz való hozzárendelésének.

**Adatformátum**

A JSON (JavaScript Object Notation) egy közös, nyelvtől független adatformátum, amely tetszőleges adatstruktúrák egyszerű szöveges ábrázolását teszi lehetővé. További információ: json.org.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

A REST API HTTP-kérelmeket az Azure Active Directory (Azure AD) védi.
Ahhoz, hogy a REST API-khoz hitelesített kérelmet lehessen készíteni, az ügyfél kódjának az API meghívása előtt érvényes hitelesítő adatokkal kell rendelkeznie a hitelesítéshez. A hitelesítés a különböző szereplők által az Azure AD-ben van koordinálva, és a hitelesítést igazoló hozzáférési jogkivonattal látja el az ügyfelet. Ezután a jogkivonat a REST API kérelmek HTTP-engedélyezési fejlécében lesz elküldve. Az Azure AD-hitelesítéssel kapcsolatos további tudnivalókért tekintse meg a [Azure Active Directory](https://portal.azure.com) fejlesztők számára című témakört.

A hozzáférési tokent a következő API-kérelmekben kell elküldeni a fejléc szakaszban:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**HTTP-kérelmek fejlécei**

Az Azure FarmBeats-adatközpont API-hívásának megadásához az alábbi leggyakoribb kérelmeket tartalmazó fejléceket kell megadni:


**Fejléc** | **Leírás és példa**
--- | ---
Content-Type  | A kérelem formátuma (Content-Type: Application/<format>) az Azure FarmBeats adatközpont API-formátuma JSON. Content-Type: Application/JSON
Engedélyezés  | Meghatározza az API-hívások létrehozásához szükséges hozzáférési jogkivonatot. **Engedélyezés: tulajdonos < hozzáférés-token >**
fogadja el | A válasz formátuma. Az Azure FarmBeats adatközponti API-k formátuma a következő **: JSON Accept (alkalmazás/JSON** )

**API-kérelmek**

REST API kérelem elvégzéséhez kombinálja a HTTP-(GET, POST, PUT vagy DELETE) metódust, az API szolgáltatás URL-címét, az erőforráshoz tartozó URI-t a lekérdezéshez, az adatküldés frissítéséhez vagy törléséhez, valamint egy vagy több HTTP-kérelem fejlécéhez.

Az API-szolgáltatás URL-címe az adatközpont URL-címe https://\<yourdatahub-website-Name >. azurewebsites. net opcionálisan lekérheti a lekérdezési paramétereket is a szűréshez, a méretének korlátozásához és a válaszokban szereplő adatok rendezéséhez.

Az alábbi példa az eszközök listájának beszerzésére szolgál:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

A legtöbb GET, POST és PUT híváshoz JSON-kérést tartalmazó törzs szükséges.

Az alábbi példa egy eszköz létrehozását mutatja be (ez egy bemeneti JSON-t tartalmaz a kérés törzsével).

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Lekérdezési paraméterek**

**Rest-** hívások esetén a kérések URI-ja alapján szűrheti, korlátozhatja és rendezheti az adatok méretét, és egy vagy több lekérdezési paramétert is megadhat. A lekérdezési paraméterekért tekintse meg az API dokumentációját, és tekintse meg az egyes GET hívásokat.
Például az eszközök listájának lekérdezésekor (hívás a/Device) a következő lekérdezési paramétereket lehet megadni:  

![A Project Farm veri](./media/for-references/query-parameters-device-1.png)

**Hibakezelés**

Az Azure FarmBeats adatközponti API-k a szabványos HTTP-hibákat adják vissza. A leggyakoribb hibakódok a következők:


 |Hibakód             | Leírás |
 |---                    | --- |
 |200                    | Sikeres |
 |201                    | Sikeres létrehozás (post) |
 |400                    | Hibás kérelem. Hiba történt a kérelemben |
 |401                    | Jogosulatlan. Az API hívója nem jogosult az erőforrás elérésére |
 |404                    | Az erőforrás nem található |
 |5XX                    | Belső kiszolgálóhiba. A 5XX-től kezdődő hibakódok azt jelzik, hogy hiba történt a kiszolgálón. További részletekért tekintse meg a kiszolgáló naplófájljait és a következő szakaszt. |


A szabványos HTTP-hibák mellett az Azure FarmBeats adatközpont API-jai a belső hibákat is visszaküldik az alábbi formátumban:

```
{
  "message": "<More information on the error>",
  "status": "<error code>”,
  "code": "<InternalErrorCode>",
  "moreInfo": "<Details of the error>"
}
```

Példa: Farm létrehozásakor a "Name" kötelező mezőt nem adták meg a bemeneti adattartalomban. Az eredményül kapott hibaüzenet a következő:

```
{
  "message": "Model validation failed",
  "status": 400,
  "code": "ModelValidationFailed",
  "moreInfo": "[\"The Name field is required.\"]"
}
```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Felhasználók vagy alkalmazások regisztrációjának hozzáadása a Azure Active Directoryhoz

 Az Azure FarmBeats API-kat egy felhasználó vagy egy alkalmazás-regisztráció is elérheti a Azure Active Directoryban. Az alábbi lépések végrehajtásával hozhat létre egy alkalmazást a Azure Active Directoryon:  

1. Lépjen a [Azure Portal](https://portal.azure.com) **Azure Active Directory, Alkalmazásregisztrációk**> **új regisztráció**elemre. Másik lehetőségként használhat egy meglévő fiókot is.
2. Új fiók esetén a következőket kell elvégeznie:

    - Név megadása
    - **Csak az ebben a szervezeti könyvtárban lévő fiókok kijelölése (egyetlen bérlő)**
    - A többi mező alapértelmezett értékei
    - **Regisztráció** kiválasztása

3. Az új/meglévő alkalmazás-regisztráció **áttekintése**után végezze el a következőket:

    - Rögzítse az **ügyfél-azonosítót** és a **bérlő azonosítóját**.
    - Nyissa meg a **tanúsítványokat és a titkokat** egy új ügyfél-titkos kód létrehozásához, és rögzítse az **ügyfél titkos kulcsát**.
    - Lépjen vissza az áttekintésre, és kattintson az **alkalmazás kezelése a helyi címtárban** elemre.
    - Válassza a **Tulajdonságok** lehetőséget az **objektumazonosító** rögzítéséhez

4. Nyissa meg az [adatközpont feladatát](https://<yourdatahub>.azurewebsites.net/swagger/index.html) , és hajtsa végre a következő lépéseket:
    - Navigáljon a **ROLEASSIGNMENT API** -hoz
    - Az imént létrehozott **objektumazonosító** RoleAssignment létrehozásához **tegye** a következőt:. – A bemeneti JSON:

  > [!NOTE]
  > A felhasználók és az AD-regisztráció hozzáadásával kapcsolatos további információkért lásd: [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) .

A fenti lépések elvégzése után az alkalmazás regisztrálása (ügyfél) egy hozzáférési jogkivonat használatával hívhatja meg az Azure FarmBeats API-kat a tulajdonosi hitelesítésen keresztül.  

A hozzáférési jogkivonat segítségével küldje el azt a következő API-kérelmekben a fejléc szakaszban, ahogy:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
