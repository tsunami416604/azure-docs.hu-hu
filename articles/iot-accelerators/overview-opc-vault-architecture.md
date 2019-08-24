---
title: OPC Vault-architektúra – Azure | Microsoft Docs
description: OPC-tár tanúsítványkezelő szolgáltatás architektúrája
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9331473402ddd22180df3b404824969360d48164
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69995814"
---
# <a name="opc-vault-architecture"></a>OPC-tár architektúrája

Ez a cikk áttekintést nyújt az **OPC Vault** -szolgáltatásról és az OPC-tároló **IoT Edge modulról**.

## <a name="overview"></a>Áttekintés

Az OPC UA-alkalmazások alkalmazás-példány-tanúsítványokat használnak az alkalmazás szintű biztonság biztosításához. A biztonságos kapcsolatok aszimmetrikus titkosítással lettek létrehozva, amelyhez az alkalmazás tanúsítványai biztosítják a nyilvános és a titkos kulcspár használatát. A tanúsítványok önaláírtak lehetnek, vagy egy hitelesítésszolgáltató (CA) által is aláírva.

Az OPC UA-alkalmazásokban szerepelnek az általa megbízhatónak ítélt alkalmazásokat jelképező megbízható tanúsítványok listája. Ezek a tanúsítványok önaláírtak, HITELESÍTÉSSZOLGÁLTATÓ aláírásával, vagy akár egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ vagy egy alárendelt HITELESÍTÉSSZOLGÁLTATÓ is lehet. Ha egy megbízható tanúsítvány egy nagyobb tanúsítványlánc részét képezi, az alkalmazás megbízik az összes olyan tanúsítványban, amely a megbízható tanúsítványok listáján a tanúsítványhoz csatlakozik, feltéve, hogy a teljes tanúsítványlánc érvényesíthető.

A megbízható önaláírt tanúsítványok és a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok megbízhatósága közötti legnagyobb különbség a megbízható üzembe helyezéshez és karbantartáshoz szükséges telepítési erőfeszítés, valamint a vállalati HITELESÍTÉSSZOLGÁLTATÓ üzemeltetésének további erőfeszítése. 

Ha egyetlen ügyfélalkalmazás használatával szeretné terjeszteni a bizalmi kapcsolatot a n-kiszolgálók önaláírt tanúsítványainak esetében, az ügyfélalkalmazás-megbízhatósági listán az összes n kiszolgálói alkalmazás tanúsítványát telepíteni kell, és az ügyfélalkalmazás tanúsítványát az összesre telepíteni kell A kiszolgálói alkalmazás megbízhatósági listája. Ez a felügyeleti erőfeszítés elég terhet jelent, és még nagyobb mennyiségű, ha a tanúsítványok élettartamát figyelembe kell venni, és a tanúsítványokat meg kell újítani.

A vállalati HITELESÍTÉSSZOLGÁLTATÓ használata nagy mértékben egyszerűsítheti a megbízhatóság kezelését több kiszolgálóval és ügyféllel. Ebben az esetben a rendszergazda egyszer létrehoz egy HITELESÍTÉSSZOLGÁLTATÓI aláírású alkalmazás példányának tanúsítványát minden olyan ügyfél és kiszolgáló esetében, amelyet használ. Emellett a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány minden alkalmazás-megbízhatósági listában telepítve van minden kiszolgálón és ügyfélen. Ezzel a módszerrel csak a lejárt tanúsítványokat kell megújítani, és cserélni az érintett alkalmazások esetében.

Az Azure Industrial IoT OPC UA tanúsítványkezelő szolgáltatása az OPC-tár szolgáltatásán alapuló OPC UA-alkalmazások vállalati szintű HITELESÍTÉSSZOLGÁLTATÓinak kezeléséhez nyújt megoldást.

Az OPC-tároló egy olyan szolgáltatást biztosít, amely egy biztonságos felhőben üzemelteti a vállalatra vonatkozó HITELESÍTÉSSZOLGÁLTATÓT, és az Azure AD által védett szolgáltatásokkal rendelkezik, Azure Key Vault hardveres biztonsági modulokkal, Cosmos DBekkel, és opcionálisan az alkalmazás-áruházként is IoT Hub.

Az OPC-tár szolgáltatás úgy lett kialakítva, hogy támogassa a szerepköralapú munkafolyamatot, ahol a személyes kérelmei aláírták az alkalmazás tanúsítványait, és ahol a biztonsági rendszergazdák és a jóváhagyók aláírási jogokkal rendelkeznek a Azure Key Vault jóváhagyása vagy elutasítása

A meglévő OPC UA GDS-alapú OT-megoldásokkal való kompatibilitás érdekében a szolgáltatások közé tartozik az OPC-tár szolgáltatás által támogatott peremhálózati modul támogatása, amely az *OPC ua globális felderítési kiszolgálót és a Tanúsítványkezelő* felületet valósítja meg a terjesztéshez. tanúsítványok és megbízhatósági kapcsolatok listája a specifikáció 12. részének megfelelően. A GDS-kiszolgáló felületét azonban még nem használják széles körben, és még korlátozott funkcionalitással rendelkeznek (olvasó szerepkör). [Igény szerint javítani fogjuk a vásárlói kérések (*) élményét](#yet-unsupported-features).

## <a name="architecture"></a>Architektúra

Az architektúra az OPC-tárolón alapuló, OPC-tárolóval IoT Edge modult használó, a gyári hálózathoz és egy webes minta UX-re épül a munkafolyamat vezérléséhez:

![OPCVault architektúra](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC Vault-szolgáltatás

Az OPC-tár szolgáltatás a következő felületekből áll: az OPC UA-alkalmazásokhoz tartozó vállalati HITELESÍTÉSSZOLGÁLTATÓ terjesztésére és kezelésére szolgáló munkafolyamat megvalósításához:

### <a name="application"></a>Alkalmazás 
- Az "OPC UA-alkalmazás" lehet egy kiszolgáló vagy egy ügyfél, vagy mindkettő. Az OPC-tároló ebben az esetben alkalmazás-regisztrációs szolgáltatóként szolgál. 
- Az alkalmazások regisztrálására, frissítésére és regisztrációjának megszüntetésére szolgáló alapműveletek mellett a keresési kifejezésekkel rendelkező alkalmazások kereséséhez és lekérdezéséhez is felületek tartoznak. 
- A tanúsítványkérelmek érvényes alkalmazásra kell hivatkozni egy kérelem feldolgozásához és az összes OPC UA-specifikus bővítménnyel rendelkező aláírt tanúsítvány kiállításához. 
- Az Application Service-t egy CosmosDB-adatbázis vagy a OpcTwin- [eszköz beállításjegyzéke (*)](#yet-unsupported-features)támogatja, az ügyfél konfigurációjától függően.

### <a name="certificate-group"></a>Tanúsítvány csoport
- A tanúsítvány-csoport egy olyan entitás, amely egy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT vagy egy alárendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt tárol, beleértve a tanúsítványok aláírására szolgáló titkos kulcsot is. 
- Az RSA-kulcs hossza, az SHA-2 kivonat hossza, valamint az élettartamok mind a kiállító HITELESÍTÉSSZOLGÁLTATÓ, mind az aláírt alkalmazás tanúsítványainak esetében konfigurálhatók. 
- Több csoport üzemeltethető egyetlen szolgáltatásban, amely lehetővé teszi a jövőbeli bővítmények használatát a https-, felhasználó-vagy ECC-algoritmusú tanúsítványsablonok [(*)](#yet-unsupported-features)használatával. 
- A HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat a rendszer a FIPS 140-2 2. szintű hardveres biztonsági modulok (HSM) által támogatott Azure Key Vault tárolja. A titkos kulcs soha nem hagyja el a biztonságos tárolót, mert az aláírást egy AzureAD által védett Key Vault művelet hajtja végre. 
- A HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok megújítható időben, és a Key Vault előzmények miatt továbbra is biztonságos tárolóhelyen maradnak. 
- Az egyes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok visszavonási listáját Key Vault titkos kulcsként is tárolja a rendszer. Az alkalmazás regisztrációjának törlése után a rendszergazda visszavonja az alkalmazás tanúsítványát is a CRL-ben.
- A kötegelt és az egyszeri tanúsítvány visszavonása támogatott.

### <a name="certificate-request"></a>Tanúsítványkérelem
A tanúsítványkérelem implementálja a munkafolyamatot, hogy új kulcspárt vagy aláírt tanúsítványt hozzon létre egy OPC UA-alkalmazáshoz tartozó "tanúsítvány-aláírási kérelem" (CSR) használatával. 
- A kérelmet egy olyan adatbázisban tárolja a rendszer, amely a tulajdonossal vagy a CSR-vel, valamint az OPC UA-alkalmazásra mutató hivatkozást tartalmaz. 
- A szolgáltatásban lévő üzleti logika érvényesíti a kérelmet az alkalmazás-adatbázisban tárolt információkkal. Az adatbázisban szereplő alkalmazás-URI-nak például meg kell egyeznie az alkalmazás URI azonosítóval a CSR-ben.
- Az aláírási jogokkal (jóváhagyó szerepkörrel) rendelkező biztonsági rendszergazda jóváhagyja vagy elutasítja a kérelmet. Ha a kérést jóváhagyják, új kulcspár és/vagy aláírt tanúsítvány jön létre. Az új titkos kulcsot a rendszer biztonságosan tárolja a kulcstartóban, miközben az új aláírt nyilvános tanúsítványt a tanúsítványkérelem adatbázisa tárolja.
- A kérelmező lekérdezheti a kérés állapotát, amíg jóvá nem hagyta vagy vissza nem vonja. Ha a kérést jóváhagyták, a titkos kulcs és a tanúsítvány az OPC UA-alkalmazás tanúsítványtárolójában tölthető le és telepíthető.
- A kérelmező mostantól elfogadhatja a szükségtelen információk törlésére vonatkozó kérelmet a kérelem-adatbázisból. 

Egy aláírt tanúsítvány élettartama során előfordulhat, hogy egy alkalmazás törölhető, vagy a kulcs biztonsága sérült lehet. Ilyen esetben a CA-kezelő a következőket teheti:
- Töröl egy alkalmazást, amely egyidejűleg törli az alkalmazás összes függőben lévő és jóváhagyott tanúsítványkérelem-kérelmét is. 
- Egy másik lehetőség, hogy csak egyetlen tanúsítványkérelmet töröl, ha csak egy kulcsot újítanak meg vagy sérülnek meg.
- A rendszer megszakította a megsérült jóváhagyott és elfogadott tanúsítványkérelmek törlését.
- A kezelők rendszeresen végrehajthatják a kiállító HITELESÍTÉSSZOLGÁLTATÓ visszavonási listájának megújítását. Az összes törölt tanúsítványkérelem visszavonása a megújítási időpontban történik, és a rendszer hozzáadja a tanúsítvány sorozatszámait a visszavont tanúsítványok listájának visszavonási listájához. Visszavont tanúsítványkérelmek vannak megjelölve visszavont tanúsítványként.
- Sürgős eseményekben az egytanúsítványos kérelmek is visszavonhatók.
- Végül a frissített CRL-ket elérhetővé kell tenni a résztvevő OPC UA-ügyfelek és-kiszolgálók számára.

Az OPC-tár szolgáltatás API-jával kapcsolatos további információkért lásd: a Service-hez készült hencegő dokumentáció.

## <a name="opc-vault-iot-edge-module"></a>OPC-tároló IoT Edge modulja
A gyári hálózat globális felderítési kiszolgálójának támogatásához az OPC-tároló modul telepíthető a peremhálózati gépen, helyi .net Core-alkalmazásként, vagy elindítható egy Docker-tárolóban. Az aktuális OPC UA .net standard szintű Auth2 érték-hitelesítés hiánya miatt az OPC-tároló Edge-moduljának funkciói egy olvasó szerepkörre korlátozódnak, mivel a felhasználó nem megszemélyesíthető az Edge-modulból a Micro Service-be az OPC UA GDS standard használatával  felület. Ezen a ponton[(*)](#yet-unsupported-features)csak azok a műveletek engedélyezettek, amelyek nem igénylik az író, a rendszergazda vagy a jóváhagyó szerepkört. 

## <a name="yet-unsupported-features"></a>Még nem támogatott funkciók

a **(*)** még nem támogatott.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC Vault architektúráját, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [OPC-tároló létrehozása és üzembe helyezése](howto-opc-vault-deploy.md)
