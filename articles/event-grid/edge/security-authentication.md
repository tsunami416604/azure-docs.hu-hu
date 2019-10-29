---
title: Biztonság és hitelesítés – Azure Event Grid IoT Edge | Microsoft Docs
description: Biztonság és hitelesítés a IoT Edge Event Gridban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 77b4b265b2e993ccdbc9e07fd2dab5a37ed22a6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992157"
---
# <a name="security-and-authentication"></a>Biztonság és hitelesítés

A biztonság és a hitelesítés egy speciális koncepció, amely először a Event Grid alapismeretekkel való ismeretét igényli. Kezdje [itt](concepts.md) , ha új IoT Edge Event Grid. Event Grid modul a IoT Edge meglévő biztonsági infrastruktúrára épül. A részletekért és a beállításért tekintse meg [ezt a dokumentációt](../../iot-edge/security.md) .

A következő szakaszok részletesen ismertetik a beállítások védelmét és hitelesítését:

* TLS-konfiguráció
* Bejövő ügyfél-hitelesítés
* Kimenő kiszolgáló hitelesítése
* Kimenő ügyfél-hitelesítés

>[!IMPORTANT]
>Event Grid modul biztonsági és hitelesítési felhasználása a IoT Edge elérhető meglévő infrastruktúra. Feltételezi, hogy IoT Edge alrendszere biztonságos.

>[!IMPORTANT]
>A Event Grid konfiguráció **alapértelmezés szerint biztonságos**. A következő alszakaszok ismertetik a hitelesítés szempontjainak felülbírálásához használható összes lehetőséget és lehetséges értéket. A módosítások elvégzése előtt Ismerje meg a hatását. A módosítások életbe léptetéséhez újra kell telepíteni a Event Grid modult.

## <a name="tls-configuration-aka-server-authentication"></a>TLS-konfiguráció (a. k. a kiszolgáló hitelesítése)

Event Grid modul HTTP-és HTTPS-végpontokat is üzemeltet. Minden IoT Edge modulhoz hozzá van rendelve egy kiszolgálói tanúsítvány a IoT Edge biztonsági démonával. A végpont védelméhez a kiszolgálói tanúsítványt használjuk. A lejáratkor a modul automatikusan frissíti a IoT Edge biztonsági démonból származó új tanúsítvánnyal.

Alapértelmezés szerint csak HTTPS-kommunikáció engedélyezett. Ezt a viselkedést **beérkező: serverAuth: tlsPolicy** Configuration használatával bírálhatja felül. A következő táblázat a tulajdonság lehetséges értékeit rögzíti.

| Lehetséges érték (ek) | Leírás |
| ---------------- | ------------ |
| Szigorú | Default (Alapértelmezett): Csak HTTPS engedélyezése
| Engedélyezve | A HTTP és a HTTPS engedélyezése
| Letiltva | Csak a HTTP engedélyezése

## <a name="inbound-client-authentication"></a>Bejövő ügyfél-hitelesítés

Az ügyfelek felügyeleti és/vagy futásidejű műveleteket végző entitások. Az ügyfelek lehetnek más IoT Edge modulok, nem IoT alkalmazások.

Event Grid modul két típusú ügyfél-hitelesítést támogat: –

* Közös hozzáférésű aláírás (SAS) kulcs alapú
* tanúsítvány alapú

Alapértelmezés szerint a Event Grid modul úgy van konfigurálva, hogy csak a tanúsítványalapú hitelesítést fogadja el. Indításkor Event Grid modul beolvassa a "TrustBundle" elemet IoT Edge biztonsági démonból, és a használatával érvényesíti az összes ügyféltanúsítványt. Az erre a láncra nem feloldható Ügyféltanúsítványok `UnAuthorized`el lesznek utasítva.

### <a name="certificate-based-client-authentication"></a>Tanúsítványalapú ügyfél-hitelesítés

A tanúsítvány alapú hitelesítés alapértelmezés szerint be van kapcsolva. A tanúsítvány alapú hitelesítést letilthatja a **beérkező: clientAuth: clientCert: enabled**tulajdonság használatával. A következő táblázat rögzíti a lehetséges értékeket (ka) t.

| Lehetséges érték (ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): Az összes kérelem beírása a Event Grid modulba az ügyféltanúsítvány bemutatása érdekében. Emellett be kell állítania a **bejövő: clientAuth: clientCert: Source**lehetőséget.
| hamis | Ne kényszerítse az ügyfelet a tanúsítvány bemutatására.

A következő táblázat a lehetséges érték (eke) t rögzíti a **bejövő: clientAuth: clientCert: Source**

| Lehetséges érték (ek) | Leírás |
| ---------------- | ------------ |
| IoT Edge | Default (Alapértelmezett): A IoT Edge Trustbundle használatával ellenőrzi az összes ügyféltanúsítványt.

Ha egy ügyfél önaláírt, alapértelmezés szerint a Event Grid modul elveti az ilyen kérelmeket. Megadhatja, hogy az önaláírt Ügyféltanúsítványok engedélyezve legyenek a **bejövő: clientAuth: clientCert: allowUnknownCA** tulajdonság használatával. A következő táblázat rögzíti a lehetséges értékeket (ka) t.

| Lehetséges érték (ek) | Leírás |
| ----------------  | ------------|
| igaz | Default (Alapértelmezett): Lehetővé teszi az önaláírt tanúsítványok sikeres megjelenítését.
| hamis | Sikertelen kérések, ha önaláírt tanúsítványok jelennek meg.

>[!IMPORTANT]
>Éles környezetekben érdemes lehet a **bejövő: clientAuth: clientCert: allowUnknownCA** **értéket**beállítani.

### <a name="sas-key-based-client-authentication"></a>SAS-kulcs alapú ügyfél-hitelesítés

A tanúsítványalapú hitelesítésen kívül a Event Grid modul is képes az SAS-kulcson alapuló hitelesítésre. Az SAS-kulcs olyan, mint a Event Grid modulban konfigurált titok, amelyet az összes bejövő hívás ellenőrzéséhez használnia kell. Az ügyfeleknek meg kell adniuk a titkot az "AEG-sas-Key" HTTP-fejlécben. Ha nem egyezik, a rendszer elutasítja a kérelmet `UnAuthorized`.

Az SAS-kulcs alapú hitelesítés vezérlésére szolgáló konfiguráció **bejövő: clientAuth: sasKeys: engedélyezve**.

| Lehetséges érték (ek) | Leírás  |
| ----------------  | ------------ |
| igaz | Lehetővé teszi az SAS-kulcson alapuló hitelesítés használatát. **Bejövő szükséges: clientAuth: sasKeys: key1** vagy **bejövő: clientAuth: sasKeys: key2**
| hamis | Default (Alapértelmezett): Az SAS-kulcs alapú hitelesítés le van tiltva.

 **bejövő: clientAuth: sasKeys: key1** és **bejövő: clientAuth: sasKeys: key2** olyan kulcsok, amelyekkel a Event Grid modult konfigurálhatja a bejövő kérések elleni kereséshez. Legalább egy kulcsot be kell állítani. A kérést kérő ügyfélnek be kell mutatnia a kulcsot az "**AEG-sas-Key**" kérelem fejlécének részeként. Ha mindkét kulcs konfigurálva van, az ügyfél a kulcsok egyikét láthatja.

> [!NOTE]
>Mindkét hitelesítési módszert konfigurálhatja. Ebben az esetben az SAS-kulcs először van bejelölve, és ha ez nem sikerül, a rendszer a tanúsítványalapú hitelesítést hajtja végre. A sikeres kéréshez csak az egyik hitelesítési módszernek kell sikeresnek lennie.

## <a name="outbound-client-authentication"></a>Kimenő ügyfél-hitelesítés

A kimenő környezetben lévő ügyfél Event Grid modulra hivatkozik. Az elvégzendő művelet az előfizetőknek nyújt eseményeket. A feliratkozási modulok a kiszolgálónak tekintendők.

Minden IoT Edge modulhoz hozzá van rendelve egy azonosító tanúsítvány a IoT Edge biztonsági démonával. A kimenő hívásokhoz a személyazonossági tanúsítványt használjuk. A lejáratkor a modul automatikusan frissíti a IoT Edge biztonsági démonból származó új tanúsítvánnyal.

A kimenő ügyfél-hitelesítés vezérlésére szolgáló konfiguráció **kimenő: clientAuth: clientCert: engedélyezve**.

| Lehetséges érték (ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): Az Event Grid modul összes kimenő kérelmét egy tanúsítvány bemutatására kéri. Be kell állítania a **kimenő: clientAuth: clientCert: Source**lehetőséget.
| hamis | Nincs szükség Event Grid modulra a tanúsítvány bemutatásához.

A tanúsítvány forrását vezérlő konfiguráció a **kimenő: clientAuth: clientCert: Source**.

| Lehetséges érték (ek) | Leírás |
| ---------------- | ------------ |
| IoT Edge | Default (Alapértelmezett): A modul IoT Edge biztonsági démon által konfigurált azonosító tanúsítványát használja.

### <a name="outbound-server-authentication"></a>Kimenő kiszolgáló hitelesítése

Egy Event Grid előfizető egyik típusa a "webhook". Alapértelmezés szerint a rendszer csak a HTTPS-végpontokat fogadja el az ilyen előfizetők számára.

A webhook cél-házirendjét a következő konfiguráció vezérli **: kimenő: webhook: httpsOnly**.

| Lehetséges érték (ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): Csak HTTPS-végponttal rendelkező előfizetőket engedélyez.
| hamis | Engedélyezi az előfizetők számára a HTTP-vagy HTTPS-végpontot.

Alapértelmezés szerint Event Grid modul érvényesíti az előfizető kiszolgálói tanúsítványát. Az érvényesítést kihagyhatja, ha felülbírálja a **kimenő: webhook: skipServerCertValidation**. A lehetséges értékek a következők: –

| Lehetséges érték (ek) | Leírás |
| ----------------  | ------------ |
| igaz | Ne érvényesítse az előfizető kiszolgálói tanúsítványát.
| hamis | Default (Alapértelmezett): Érvényesítse az előfizető kiszolgálói tanúsítványát.

Ha az előfizető tanúsítványa önaláírt, akkor alapértelmezés szerint Event Grid modul elveti az ilyen előfizetőket. Ha engedélyezni szeretné az önaláírt tanúsítványt, felülbírálhatja a **kimenő: webhook: allowUnknownCA**. A következő táblázat rögzíti a lehetséges értékeket (ka) t.

| Lehetséges érték (ek) | Leírás |
| ----------------  | ------------ |
| igaz | Default (Alapértelmezett): Lehetővé teszi az önaláírt tanúsítványok sikeres megjelenítését.
| hamis | Sikertelen kérések, ha önaláírt tanúsítványok jelennek meg.

>[!IMPORTANT]
>Éles környezetben a **kimenő: webhook: allowUnknownCA** **false értéket**kell beállítania.

> [!NOTE]
>IoT Edge környezet önaláírt tanúsítványokat hoz létre. Javasoljuk, hogy a jóváhagyott hitelesítésszolgáltatók által kiadott tanúsítványokat előállítson a termelési számítási feladatokhoz, és állítsa a **allowUnknownCA** tulajdonságot mind a bejövő, mind a **kimenő értékre.**

## <a name="summary"></a>Összefoglalás

Az Event Grid-modul **alapértelmezés szerint biztonságos**. Javasoljuk, hogy ezeket az alapértelmezéseket az éles környezetekben is megőrizze.

A konfigurálás során a következő irányadó elveket kell használni: –

* Csak HTTPS-kérelmek engedélyezése a modulnak.
* Csak tanúsítvány alapú ügyfél-hitelesítés engedélyezése. Csak a jól ismert hitelesítésszolgáltatók által kiadott tanúsítványokat engedélyezze. Önaláírt tanúsítványok letiltása.
* SASKey-alapú ügyfél-hitelesítés letiltása.
* Mindig jelen van Event Grid modul azonosító tanúsítványa a kimenő hívásokon.
* Csak HTTPS-előfizetők engedélyezése webhook-célhelyek számára.
* Mindig ellenőrizze az előfizető kiszolgálói tanúsítványát a webhook-célhelyek típusaihoz. Csak a jól ismert hitelesítésszolgáltatók által kiadott tanúsítványok engedélyezése. Önaláírt tanúsítványok letiltása.

Alapértelmezés szerint a Event Grid modul a következő konfigurációval van telepítve: –

 ```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
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
