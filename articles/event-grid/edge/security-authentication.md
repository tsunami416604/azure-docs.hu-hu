---
title: Biztonság és hitelesítés – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Biztonság és hitelesítés az IoT Edge Eseményrácsban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 5dfa17fd702b76e2cfaa7a91066dbc6749c1069e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844513"
---
# <a name="security-and-authentication"></a>Biztonság és hitelesítés

A biztonság és a hitelesítés egy speciális fogalom, és először az Event Grid alapjainak ismeretét igényli. Kezdje [itt,](concepts.md) ha most új az Event Grid az IoT Edge-en. Event Grid modul az IoT Edge meglévő biztonsági infrastruktúrájára épül. A részleteket és a beállításokat ebben a [dokumentációban](../../iot-edge/security.md) találja.

A következő szakaszok részletesen ismertetik, hogyan vannak biztonságosak és hitelesíthetők ezek a beállítások:

* TLS-konfiguráció
* Bejövő ügyfél-hitelesítés
* Kimenő kiszolgáló hitelesítése
* Kimenő ügyfélhitelesítés

>[!IMPORTANT]
>Event Grid modul biztonsági és hitelesítési tőkeáttétel a meglévő infrastruktúra elérhető IoT Edge. A feltételezés az, hogy az IoT Edge alrendszer biztonságos.

>[!IMPORTANT]
>Az Event Grid **konfigurációja alapértelmezés szerint biztonságos.** A következő alszakaszok ismertetik az összes lehetőséget és lehetséges értéket, amelyet a hitelesítés szempontjainak felülbírálására használhat. A módosítások végrehajtása előtt ismerje meg a hatást. Ahhoz, hogy bármilyen változás érvénybe lépjen, az Event Grid modult újra kell telepíteni.

## <a name="tls-configuration-aka-server-authentication"></a>TLS-konfiguráció (más néven kiszolgáló hitelesítése)

Az Event Grid modul HTTP- és HTTPS-végpontokat is üzemeltet. Minden IoT Edge-modulhoz az IoT Edge biztonsági démona kiszolgálói tanúsítványt rendel hozzá. A végpont védelméhez a kiszolgálói tanúsítványt használjuk. Lejáratkor a modul automatikusan frissül az IoT Edge biztonsági démon új tanúsítványával.

Alapértelmezés szerint csak https-kommunikáció engedélyezett. Ezt a viselkedést **inbound__serverAuth__tlsPolicy** konfigurációval felülbírálhatja. Az alábbi táblázat a tulajdonság lehetséges értékét (értékeit) tartalmazza.

| Lehetséges érték(ek) | Leírás |
| ---------------- | ------------ |
| Szigorú | Default (Alapértelmezett): Csak https engedélyezése
| Engedélyezve | Http és HTTPS engedélyezése
| Letiltva | Csak http engedélyezése

## <a name="inbound-client-authentication"></a>Bejövő ügyfél-hitelesítés

Az ügyfelek felügyeleti és/vagy futásidejű műveleteket végző entitások. Az ügyfelek lehetnek más IoT Edge-modulok, nem IoT-alkalmazások.

Az Event Grid modul kétféle ügyfélhitelesítést támogat:

* Megosztott hozzáférésű aláírás (SAS) kulcsalapú
* tanúsítvány alapú

Alapértelmezés szerint az Event Grid modul úgy van beállítva, hogy csak tanúsítványalapú hitelesítést fogadjon el. Indításkor az Event Grid modul lekéri a "TrustBundle" az IoT Edge biztonsági démon, és használja, hogy érvényesítse az ügyfél tanúsítvány. Azok az ügyféltanúsítványok, amelyek nem oldódnak fel ezzel a lánccal, a program elutasítja a programot. `UnAuthorized`

### <a name="certificate-based-client-authentication"></a>Tanúsítványalapú ügyfélhitelesítés

A tanúsítványalapú hitelesítés alapértelmezés szerint be van kapcsolva. A tanúsítványalapú hitelesítést letilthatja a tulajdonságon keresztül **inbound__clientAuth__clientCert__enabled.** Az alábbi táblázat a lehetséges értékeket (értékeket) rögzíti.

| Lehetséges érték(ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): Az ügyféltanúsítvány bemutatásához az Event Grid modul összes kérelmeszükséges. Ezenkívül konfigurálnia kell **inbound__clientAuth__clientCert__source.**
| hamis | Ne kényszerítse az ügyfelet tanúsítvány bemutatására.

Az alábbi táblázat a inbound__clientAuth__clientCert__source lehetséges értékét (értékeit) **tartalmazza.**

| Lehetséges érték(ek) | Leírás |
| ---------------- | ------------ |
| IoT Edge | Default (Alapértelmezett): Az IoT Edge megbízhatósági átengedése az összes ügyféltanúsítvány érvényesítéséhez.

Ha egy ügyfél önaláírt, alapértelmezés szerint az Event Grid modul elutasítja az ilyen kéréseket. Dönthet úgy, hogy engedélyezi az önaláírt ügyféltanúsítványokat **inbound__clientAuth__clientCert__allowUnknownCA** tulajdonon keresztül. Az alábbi táblázat a lehetséges értékeket (értékeket) rögzíti.

| Lehetséges érték(ek) | Leírás |
| ----------------  | ------------|
| igaz | Default (Alapértelmezett): Lehetővé teszi az önaláírt tanúsítványok sikeres bemutatását.
| hamis | Sikertelen kérelmek, ha önaláírt tanúsítványok jelennek meg.

>[!IMPORTANT]
>Éles környezetben előfordulhat, hogy **a inbound__clientAuth__clientCert__allowUnknownCA** **hamisra**szeretné állítani.

### <a name="sas-key-based-client-authentication"></a>SAS kulcsalapú ügyfélhitelesítés

A tanúsítványalapú hitelesítés mellett az Event Grid modul sas-kulcsalapú hitelesítést is eltud végezni. A SAS-kulcs olyan, mint egy titkos kulcs az Event Grid modulban konfigurálva, amelyet az összes bejövő hívás érvényesítéséhez kell használnia. Az ügyfeleknek meg kell adniuk a titkos kulcsot az "aeg-sas-key" HTTP-fejlécben. A kérést a `UnAuthorized` rendszer elutasítja, ha az nem egyezik.

A SAS-kulcsalapú hitelesítés vezérlésének konfigurációja **inbound__clientAuth__sasKeys__enabled.**

| Lehetséges érték(ek) | Leírás  |
| ----------------  | ------------ |
| igaz | SAS-kulcsalapú hitelesítést tesz lehetővé. **inbound__clientAuth__sasKeys__key1** vagy **inbound__clientAuth__sasKeys__key2** igényel
| hamis | Default (Alapértelmezett): A SAS-kulcsalapú hitelesítés le van tiltva.

 **inbound__clientAuth__sasKeys__key1** és **inbound__clientAuth__sasKeys__key2** olyan kulcsok, amelyek segítségével az Event Grid modult úgy konfigurálja, hogy ellenőrizze a bejövő kéréseket. Legalább az egyik kulcsot konfigurálni kell. A kérést végző ügyfélnek a kérés fejlécének**aeg-sas-key**részeként be kell mutatnia a kulcsot. Ha mindkét kulcs konfigurálva van, az ügyfél bármelyik kulcsot bemutathatja.

> [!NOTE]
>Mindkét hitelesítési módszert konfigurálhatja. Ebben az esetben a Rendszer először ellenőrzi a SAS-kulcsot, és csak akkor, ha ez nem sikerül, a tanúsítványalapú hitelesítés végrehajtása történik. Ahhoz, hogy a kérelem sikeres legyen, csak az egyik hitelesítési módszernek kell sikeresnek lennie.

## <a name="outbound-client-authentication"></a>Kimenő ügyfélhitelesítés

A kimenő környezetben lévő ügyfél az Eseményrács modulra hivatkozik. A művelet folyamatban van az események kézbesítése az előfizetőknek. Az előfizetési modulok tekinthetők kiszolgálónak.

Minden IoT Edge-modul hoz egy identitás-tanúsítványt az IoT Edge biztonsági démon. Az identitás-tanúsítványt használjuk a kimenő hívásokhoz. Lejáratkor a modul automatikusan frissül az IoT Edge biztonsági démon új tanúsítványával.

A kimenő ügyfél-hitelesítés vezérlésére outbound__clientAuth__clientCert__enabled **konfiguráció.**

| Lehetséges érték(ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): A tanúsítvány bemutatásához az Event Grid modul összes kimenő kérelme szükséges. Konfigurálnia kell **a outbound__clientAuth__clientCert__source.**
| hamis | Nincs szükség az Event Grid modulra a tanúsítvány bemutatásához.

A tanúsítvány forrását vezérlő **konfigurációt outbound__clientAuth__clientCert__source.**

| Lehetséges érték(ek) | Leírás |
| ---------------- | ------------ |
| IoT Edge | Default (Alapértelmezett): A modul identitástanúsítványát használja, amelyet az IoT Edge biztonsági démon konfigurált.

### <a name="outbound-server-authentication"></a>Kimenő kiszolgáló hitelesítése

Az Event Grid-előfizetők egyik céltípusa a "Webhook". Alapértelmezés szerint csak HTTPS-végpontok fogadhatók el az ilyen előfizetők számára.

A webhook **célházirendjének**outbound__webhook__httpsOnly.

| Lehetséges érték(ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): Csak HTTPS-végponttal rendelkező előfizetők számára engedélyezi.
| hamis | Lehetővé teszi a HTTP- vagy HTTPS-végponttal rendelkező előfizetők számára.

Alapértelmezés szerint az Event Grid modul ellenőrzi az előfizető kiszolgálói tanúsítványát. Az érvényesítést a **outbound__webhook__skipServerCertValidation**felülbírálásával hagyhatja ki. Lehetséges értékek:

| Lehetséges érték(ek) | Leírás |
| ----------------  | ------------ |
| igaz | Ne érvényesítse az előfizető kiszolgálói tanúsítványát.
| hamis | Default (Alapértelmezett): Az előfizető kiszolgálói tanúsítványának ellenőrzése.

Ha az előfizető identifikáta van aláírva, akkor alapértelmezés szerint az Event Grid modul elutasítja az ilyen előfizetőket. Az önaláírt tanúsítvány engedélyezéséhez felülbírálhatja **outbound__webhook__allowUnknownCA.** Az alábbi táblázat a lehetséges értékeket (értékeket) rögzíti.

| Lehetséges érték(ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): Lehetővé teszi az önaláírt tanúsítványok sikeres bemutatását.
| hamis | Sikertelen kérelmek, ha önaláírt tanúsítványok jelennek meg.

>[!IMPORTANT]
>Éles környezetben a **outbound__webhook__allowUnknownCA** **hamis**értékűre kell állítania.

> [!NOTE]
>Az IoT Edge-környezet önaláírt tanúsítványokat hoz létre. Az ajánlott az engedélyezett hitelesítésszolgáltatóáltal az éles számítási feladatokhoz kiadott tanúsítványok létrehozása, és **az allowUnknownCA** tulajdonság beállítása a bejövő és a kimenő értéken **is hamis.**

## <a name="summary"></a>Összefoglalás

Az Event Grid modul **alapértelmezés szerint biztonságos.** Azt javasoljuk, hogy ezeket az alapértelmezett az éles környezetben.

A konfigurálás során az alábbi vezérelveket kell használni:

* Csak HTTPS-kérelmek engedélyezése a modulba.
* Csak tanúsítványalapú ügyfélhitelesítés engedélyezése. Csak a jól ismert hitelesítésszolgáltató által kiállított tanúsítványok engedélyezése. Az önaláírt tanúsítványok leengedése.
* SASKey alapú ügyfélhitelesítés leengedése.
* Az Event Grid modul identitástanúsítványának mindig jelenjenek meg a kimenő hívásokon.
* Csak HTTPS-előfizetők engedélyezése a Webhook céltípusaihoz.
* Mindig érvényesítse az előfizető kiszolgálói tanúsítványát a Webhook céltípusaihoz. Csak a jól ismert hitelesítésszolgáltató által kiállított tanúsítványok engedélyezése. Az önaláírt tanúsítványok leengedése.

Alapértelmezés szerint az Event Grid modul a következő konfigurációval van telepítve:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
