---
title: OPC Vault architektúra - Azure | Microsoft dokumentumok
description: OPC Vault tanúsítványkezelő szolgáltatás architektúrája
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "71200158"
---
# <a name="opc-vault-architecture"></a>OPC Vault architektúra

Ez a cikk áttekintést nyújt az OPC Vault mikroszolgáltatásról és az OPC Vault IoT Edge modulról.

Az OPC ua alkalmazások alkalmazáspéldány-tanúsítványokat használnak az alkalmazásszintű biztonság biztosításához. A biztonságos kapcsolat aszimmetrikus titkosítással jön létre, amelyhez az alkalmazástanúsítványok biztosítják a nyilvános és a személyes kulcspárt. A tanúsítványok lehetnek önaláírtak, vagy egy hitelesítésszolgáltató (CA) írható alá.

Az OPC Felhasználói felületi azonosító alkalmazás rendelkezik a megbízható alkalmazásokat jelölő megbízható tanúsítványok listájával. Ezek a tanúsítványok lehetnek önaláírtak vagy aláírhatók egy hitelesítésszolgáltató által, vagy lehetnek legfelső szintű hitelesítésszolgáltató vagy maguk alhitelesítésszolgáltató. Ha egy megbízható tanúsítvány egy nagyobb tanúsítványlánc része, az alkalmazás megbízik az összes olyan tanúsítványban, amely a megbízhatósági listában szereplő tanúsítványig láncol. Ez mindaddig igaz, amíg a teljes tanúsítványlánc érvényesíthető.

Az önaláírt tanúsítványok megbízhatósága és a hitelesítésszolgáltatói tanúsítványok megbízhatósága közötti fő különbség a megbízhatóság üzembe helyezéséhez és fenntartásához szükséges telepítési munka. További erőfeszítéseket is tesz a vállalatspecifikus hitelesítésszolgáltató üzemeltetésére. 

Ha egyetlen ügyfélalkalmazással több kiszolgálóra szeretne megbízhatósági kapcsolatot terjeszteni, telepítenie kell az összes kiszolgálóalkalmazás-tanúsítványt az ügyfélalkalmazás megbízhatósági listájára. Ezenkívül telepítenie kell az ügyfélalkalmazás-tanúsítványt az összes kiszolgálóalkalmazás-megbízhatósági listára. Ez az adminisztratív erőfeszítés elég nagy terhet jelent, és még akkor is növekszik, ha figyelembe kell vennie a tanúsítványok élettartamát és meg kell újítania a tanúsítványokat.

A vállalatspecifikus hitelesítésszolgáltató használata nagymértékben leegyszerűsítheti a több kiszolgálóval és ügyféllel való bizalmi kapcsolat kezelését. Ebben az esetben a rendszergazda minden használt ügyfélhez és kiszolgálóhoz egyszer létrehoz egy hitelesítésszolgáltató által aláírt alkalmazáspéldány-tanúsítványt. Ezenkívül a hitelesítésszolgáltatói tanúsítvány minden alkalmazásmegbízhatósági listára telepítve van, minden kiszolgálón és ügyfélen. Ezzel a megközelítéssel csak a lejárt tanúsítványokat kell megújítani, és cserélni az érintett alkalmazások.

Az Azure Industrial IoT OPC UA tanúsítványkezelési szolgáltatás segít az OPC UA-alkalmazások vállalatspecifikus hitelesítésszolgáltatójának kezelésében. Ez a szolgáltatás az OPC Vault mikroszolgáltatáson alapul. Az OPC Vault mikroszolgáltatást biztosít egy vállalatspecifikus hitelesítésszolgáltató biztonságos felhőben való üzemeltetéséhez. Ezt a megoldást az Azure Active Directory (Azure AD), az Azure Key Vault hardveres biztonsági modulokkal (HSMs), az Azure Cosmos DB és opcionálisan az IoT Hub alkalmazástárolója által védett szolgáltatások is támogatja.

Az OPC Vault mikroszolgáltatás célja, hogy támogassa a szerepköralapú munkafolyamat, ahol a biztonsági rendszergazdák és jóváhagyók aláírási jogokkal az Azure Key Vault jóváhagyja vagy elutasítja a kérelmeket.

A meglévő OPC UA-megoldásokkal való kompatibilitás érdekében a szolgáltatások tartalmazzák az OPC Vault mikroszolgáltatás-alapú peremhálózati modul támogatását. Ezzel megvalósítja az **OPC UA Global Discovery Server és a Certificate Management** felületet, így a specifikáció 12. 


## <a name="architecture"></a>Architektúra

Az architektúra az OPC Vault mikroszolgáltatáson alapul, egy OPC Vault IoT Edge modullal a gyári hálózathoz és egy webes minta UX-t a munkafolyamat vezérléséhez:

![OpC Vault architektúra diagramja](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC-tároló mikroszolgáltatása

Az OPC Vault mikroszolgáltatás a következő felületekből áll, amelyek megvalósítják a munkafolyamatot az OPC UA-alkalmazások vállalatspecifikus hitelesítésszolgáltatójának terjesztéséhez és kezeléséhez.

### <a name="application"></a>Alkalmazás 
- Az OPC UA-alkalmazások lehetnek kiszolgálók vagy ügyfélalkalmazások, vagy mindkettő. Az OPC Vault ebben az esetben alkalmazásregisztrációs hatóságként szolgál. 
- Az alkalmazások regisztrálásához, frissítéséhez és regisztrációjának lekérdezéséhez az alkalmazások regisztrálásához, frissítéséhez és regisztrációjának megszüntetése érdekében olyan felületek is vannak, amelyek keresési kifejezésekkel rendelkező alkalmazásokat keresnek és kérdeznek le. 
- A tanúsítványkérelmeknek érvényes kérelemre kell hivatkozniuk a kérelem feldolgozásához és az OPC UA-specifikus összes bővítményével rendelkező aláírt tanúsítvány kiállításához. 
- Az alkalmazásszolgáltatás az Azure Cosmos DB adatbázisa.

### <a name="certificate-group"></a>Tanúsítványcsoport
- A tanúsítványcsoport olyan entitás, amely legfelső szintű hitelesítésszolgáltatót vagy alhitelesítési tanúsítványt tárol, beleértve a tanúsítványok aláírásához szükséges személyes kulcsot is. 
- Az RSA-kulcs hossza, az SHA-2 kivonathossza és élettartama konfigurálható mind a kiállító hitelesítésszolgáltatóhoz, mind az aláírt alkalmazástanúsítványokhoz. 
- A hitelesítésszolgáltatói tanúsítványokat az Azure Key Vaultban tárolja, fips 140-2 2 szintű HSM-mel támogatva. A személyes kulcs soha nem hagyja el a biztonságos tárolót, mert az aláírást az Azure AD által védett Key Vault-művelet végzi. 
- A hitelesítésszolgáltatói tanúsítványokat idővel megújíthatja, és a Key Vault előzményei miatt biztonságos tárolóban maradhatnak. 
- Az egyes hitelesítésszolgáltatói tanúsítványok visszavonási listája a Key Vaultban is titkos kulcsként tárolódik. Ha egy alkalmazás nincs regisztrálva, az alkalmazástanúsítványt a rendszergazda is visszavonja a visszavont tanúsítványok listájában.
- Az egyes és kötegelt tanúsítványok at visszavonhatja.

### <a name="certificate-request"></a>Tanúsítványkérelem
A tanúsítványkérelem egy opc ua alkalmazás tanúsítványaláírási kérelem (CSR) használatával valósítja meg a munkafolyamatot egy új kulcspár vagy aláírt tanúsítvány létrehozásához. 
- A kérelem egy adatbázisban tárolódik, amely tartalmazza a kapcsolódó információkat, például a tárgyat vagy a CSR-t, valamint az OPC UA alkalmazásra való hivatkozást. 
- A szolgáltatás üzleti logikája érvényesíti a kérelmet az alkalmazás-adatbázisban tárolt adatokkal szemben. Például az alkalmazás Uri az adatbázisban meg kell egyeznie az alkalmazás Uri a CSR-ben.
- Az aláírási jogosultságokkal (azaz a Jóváhagyó szerepkörrel) rendelkező biztonsági rendszergazda jóváhagyja vagy elutasítja a kérelmet. Ha a kérelmet jóváhagyták, új kulcspár vagy aláírt tanúsítvány (vagy mindkettő) jön létre. Az új személyes kulcs biztonságosan tárolódik a Key Vaultban, és az új aláírt nyilvános tanúsítvány a tanúsítványkérelem-adatbázisban tárolódik.
- A kérelmező lekérheti a kérelem állapotát, amíg jóvá nem hagyják vagy vissza nem vonják. Ha a kérelmet jóváhagyták, a személyes kulcs és a tanúsítvány letölthető és telepíthető az OPC UA alkalmazás tanúsítványtárolójában.
- A kérelmező most már elfogadhatja a szükségtelen adatok törlésére vonatkozó kérést a kérelemadatbázisból. 

Az aláírt tanúsítvány élettartama alatt előfordulhat, hogy egy alkalmazás törlődik, vagy egy kulcs biztonsága sérülhet. Ilyen esetben a hitelesítésigazgató:
- Töröljön egy alkalmazást, amely az alkalmazás összes függőben lévő és jóváhagyott tanúsítványkérelmét is törli. 
- Csak egyetlen tanúsítványkérelmet töröljön, ha csak egy kulcs megújítása vagy feltörése történik.

Most a feltört jóváhagyott és elfogadott tanúsítványkérelmek töröltként vannak megjelölve.

A kezelő rendszeresen megújíthatja a kiállító hitelesítéshitelesítéssel kapcsolatos visszavonási felhívást. A megújítás időpontjában az összes törölt tanúsítványkérelmet visszavonják, és a tanúsítvány sorozatszámai hozzáadódnak a visszavont tanúsítványok listájához. A visszavont tanúsítványkérelmek visszavonásra van békülve. Sürgős események esetén az egyes tanúsítványkérelmek is visszavonhatók.

Végül a frissített visszavonási adatok terjeszthetőek a részt vevő OPC UA-ügyfelek és -kiszolgálók számára.

## <a name="opc-vault-iot-edge-module"></a>OPC-tároló IoT Edge modul
A globális felderítési kiszolgáló üzemi hálózatának támogatásához telepítheti az OPC Vault modult a peremhálózaton. Futtassa helyi .NET Core alkalmazásként, vagy indítsa el egy Docker-tárolóban. Vegye figyelembe, hogy az aktuális OPC UA .NET standard verem auth2 hitelesítési támogatásának hiánya miatt az OPC Vault peremmodul funkciója egy Olvasó szerepkörre korlátozódik. A felhasználó nem személyesíthető megszemélyesítve a peremhálózati modulról a mikroszolgáltatásra az OPC UA GDS szabványos felülethasználatával.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az OPC Vault architektúrával, a következőket teheti:

> [!div class="nextstepaction"]
> [OpC Vault létrehozása és üzembe helyezése](howto-opc-vault-deploy.md)
