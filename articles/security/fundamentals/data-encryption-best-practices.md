---
title: Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások – Microsoft Azure
description: Ez a cikk az adatbiztonságra és-titkosításra vonatkozó ajánlott eljárásokat ismerteti az Azure beépített képességeinek használatával.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 83b4f2fce3dbae2168627194a45e62a2d4479936
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934747"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Az Azure adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárásai
Ez a cikk az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárásokat ismerteti.

Az ajánlott eljárások a vélemények konszenzusán alapulnak, és a jelenlegi Azure platform-képességekkel és-szolgáltatásokkal működnek. A vélemények és technológiák idővel változnak, és ez a cikk rendszeresen frissül, hogy tükrözze ezeket a módosításokat.

## <a name="protect-data"></a>Adatok védelme
A felhőben tárolt adatainak védelme érdekében figyelembe kell vennie azokat a lehetséges állapotokat, amelyekben az adatai megjelenhetnek, és hogy milyen vezérlők érhetők el az adott állapotban. Az Azure-Adatbiztonság és-titkosítás ajánlott eljárásai a következő adatállapotokhoz kapcsolódnak:

- Nyugalmi állapotban: Ez magában foglalja az összes olyan adattároló-objektumot, tárolót és típust, amelyek statikusan léteznek fizikai adathordozón, akár mágneses, akár optikai lemez esetén.
- Tranzitban: Ha az adatátvitelt az összetevők, a helyszínek vagy a programok között végzik, az átvitel folyamatban van. Ilyenek például a hálózaton keresztüli átvitel (a helyszíniről a felhőbe és fordítva, beleértve a hibrid kapcsolatokat, például a ExpressRoute), vagy egy bemeneti/kimeneti folyamat során.

## <a name="choose-a-key-management-solution"></a>Kulcskezelő megoldás kiválasztása

A kulcsok védelme elengedhetetlen a felhőben tárolt adatok védelméhez.

[Azure Key Vault](/azure/key-vault/key-vault-overview) segít megőrizni a Felhőbeli alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkokat. A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők percek alatt hozhatnak létre kulcsokat a fejlesztéshez és teszteléshez, majd áttelepíthetik őket az éles kulcsokra. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

A Key Vault használatával több biztonságos tárolót is létrehozhat, amelyek neve: Vaults. Ezeket a tárolókat a HSM támogatja. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A Key Vault a bennük tárolt bármit is szabályozhatja és naplózhatja. A Azure Key Vault képes kezelni Transport Layer Security (TLS) tanúsítványok kérését és megújítását. Hatékony megoldást kínál a tanúsítványok életciklusának kezelésére.

Azure Key Vault úgy lett kialakítva, hogy támogassa az alkalmazás kulcsait és titkos kulcsait. A Key Vault nem a felhasználói jelszavak tárolására szolgál.

Az alábbiakban a Key Vault használatára vonatkozó ajánlott biztonsági eljárásokat ismertetjük.

**Ajánlott eljárás**: Hozzáférést biztosíthat a felhasználóknak, csoportoknak és alkalmazásoknak egy adott hatókörben.   
**Részletek**: A RBAC előre definiált szerepköreinek használata. Ha például hozzáférést szeretne biztosítani egy felhasználónak a kulcstartók felügyeletéhez, akkor az előre definiált szerepkört [Key Vault](/azure/role-based-access-control/built-in-roles) a felhasználóhoz egy adott hatókörben. A hatókör ebben az esetben egy előfizetés, egy erőforráscsoport vagy csak egy adott kulcstartó. Ha az előre definiált szerepkörök nem felelnek meg az igényeinek, [saját szerepköröket is meghatározhat](/azure/role-based-access-control/custom-roles).

**Ajánlott eljárás**: Annak szabályozása, hogy mely felhasználók férhetnek hozzá.   
**Részletek**: A kulcstartó-hozzáférés vezérlése két külön felületen, a felügyeleti síkon és az adatsíkon keresztül történik. A felügyeleti sík és az adatsík hozzáférés-vezérlése egymástól függetlenül működik.

A RBAC használatával szabályozhatja, hogy mely felhasználók férhetnek hozzá. Ha például egy kulcstartóban szeretné megadni az alkalmazáshoz a kulcsok használatát, csak a Key Vault hozzáférési házirendjének használatával kell megadnia az adatsíkok hozzáférési engedélyeit, és ehhez az alkalmazáshoz nincs szükség felügyeleti síkon való hozzáférésre. Ha azt szeretné, hogy a felhasználók beolvassák a tároló tulajdonságait és címkéit, de nem rendelkeznek hozzáféréssel a kulcsokhoz, a titkokhoz vagy a tanúsítványokhoz, a felhasználó számára az RBAC használatával olvasási hozzáférést biztosíthat, és nem szükséges az adatsíkon való hozzáférés.

**Ajánlott eljárás**: Tárolja a tanúsítványokat a kulcstartóban. A tanúsítványok nagy értékűek. Rossz kezekben az alkalmazás biztonsága vagy az adatai biztonsága is sérülhet.   
**Részletek**: Azure Resource Manager a virtuális gépek üzembe helyezésekor biztonságosan telepíthetik a Azure Key Vaultban tárolt tanúsítványokat az Azure-beli virtuális gépekre. A Key Vault megfelelő hozzáférési házirendjeinek beállításával azt is szabályozhatja, hogy ki férhet hozzá a tanúsítványhoz. Egy másik előny, hogy az összes tanúsítványát egy helyen kezelheti Azure Key Vaultban. További információért lásd: [tanúsítványok központi telepítése virtuális gépekre az ügyfél által felügyelt Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) .

**Ajánlott eljárás**: Győződjön meg arról, hogy helyreállíthatja a kulcstartók vagy kulcstartó-objektumok törlését.   
**Részletek**: A kulcstartók vagy a Key Vault-objektumok törlése véletlen vagy rosszindulatú lehet. Engedélyezze a Key Vault Soft delete és Purge Protection szolgáltatásait, különösen az inaktív adatok titkosításához használt kulcsokat. A kulcsok törlése egyenértékű az adatvesztéssel, így szükség esetén helyreállíthatja a törölt tárolókat és a tár objektumait. Rendszeresen Key Vault helyreállítási műveleteket.

> [!NOTE]
> Ha a felhasználó közreműködői engedélyekkel (RBAC) rendelkezik egy kulcstároló-felügyeleti síkon, a kulcstartó-hozzáférési házirend beállításával saját maguk is megadhatják az adatsíkon való hozzáférést. Azt javasoljuk, hogy szigorúan szabályozzák, hogy kik férjenek hozzá a kulcstartóhoz, így biztosítható, hogy csak a jogosult személyek férhessenek hozzá és kezelhetik a kulcstartókat, a kulcsokat, a titkokat és a tanúsítványokat.
>
>

## <a name="manage-with-secure-workstations"></a>Felügyelet biztonságos munkaállomásokkal

> [!NOTE]
> Az előfizetés rendszergazdájának vagy tulajdonosának biztonságos hozzáférési munkaállomást vagy emelt szintű hozzáférési munkaállomást kell használnia.
>
>

Mivel a támadások túlnyomó többsége a végfelhasználót célozza meg, a végpont a támadás egyik elsődleges pontja lesz. A végpontot veszélyeztető támadók a felhasználó hitelesítő adataival férhetnek hozzá a szervezet adataihoz. A legtöbb végponti támadás kihasználja azt a tényt, hogy a felhasználók a helyi munkaállomáson rendszergazdák.

**Ajánlott eljárás**: Használjon biztonságos felügyeleti munkaállomást a bizalmas fiókok, feladatok és adatok védelméhez.   
**Részletek**: A munkaállomások támadási felületének csökkentéséhez használjon rendszerjogosultságú [hozzáférési](https://technet.microsoft.com/library/mt634654.aspx) munkaállomást. Ezek a biztonságos felügyeleti munkaállomások segíthetnek csökkenteni a támadások némelyikét, és biztosítják, hogy az adatai biztonságban legyenek.

**Ajánlott eljárás**: Győződjön meg arról, hogy az Endpoint Protection.   
**Részletek**: A biztonsági szabályzatok betartatása minden olyan eszközön, amely az adattárolásra szolgál, függetlenül az adathelytől (Felhőbeli vagy helyszíni).

## <a name="protect-data-at-rest"></a>Adatok védelme nyugalmi állapotban

Az inaktív [adatok titkosítása](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) az adatvédelem, a megfelelőség és az adatok szuverenitásának kötelező lépése.

**Ajánlott eljárás**: Alkalmazzon lemezes titkosítást az adatai védelmére.   
**Részletek**: [Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview)használata. Lehetővé teszi a rendszergazdáknak a Windows-és Linux-IaaS VM-lemezek titkosítását. A lemezes titkosítás ötvözi az iparági szabványnak megfelelő Windows BitLocker szolgáltatást és a Linux dm-crypt funkciót, hogy mennyiségi titkosítást biztosítson az operációs rendszer és az adatlemezek számára.

Az Azure Storage és a Azure SQL Database alapértelmezés szerint titkosítja az inaktív adatok mennyiségét, és számos szolgáltatás lehetőséget biztosít a titkosításra. A Azure Key Vault használatával megtarthatja az adatait elérő és titkosító kulcsok felügyeletét. [További információért lásd az Azure erőforrás-szolgáltatók titkosítási modell támogatását](encryption-atrest.md#azure-resource-providers-encryption-model-support)ismertető témakört.

**Ajánlott eljárások**: A titkosítás használatával csökkentheti a jogosulatlan adathozzáféréshez kapcsolódó kockázatokat.   
**Részletek**: Titkosítsa a meghajtókat, mielőtt bizalmas adatokat írna rájuk.

Azok a szervezetek, amelyek nem kényszerítik az adattitkosítást, jobban ki vannak téve az adatok bizalmasságával kapcsolatos problémáknak. Előfordulhat például, hogy a jogosulatlan vagy a szélhámos felhasználók ellopják a feltört fiókokban lévő adatvesztést, vagy jogosulatlan hozzáférést szereznek az adatkódoláshoz. A vállalatoknak azt is bizonyítaniuk kell, hogy szorgalmasak, és a megfelelő biztonsági ellenőrzésekkel javítják az adatbiztonságot az iparági előírásoknak való megfelelés érdekében.

## <a name="protect-data-in-transit"></a>Adatátviteli védelem

Az adattovábbítási adatok védelmének fontos részét kell képeznie az adatvédelmi stratégiának. Mivel az adatok több helyről térnek vissza és oda, általában azt javasoljuk, hogy mindig SSL/TLS protokollt használjon az adatok különböző helyszíneken való cseréjéhez. Bizonyos esetekben érdemes lehet elkülöníteni a teljes kommunikációs csatornát a helyszíni és a felhőalapú infrastruktúrák között egy VPN használatával.

A helyszíni infrastruktúra és az Azure közötti adatátvitelhez érdemes megfontolnia a megfelelő védelmet, például a HTTPS-t vagy a VPN-t. Amikor titkosított forgalmat küld egy Azure-beli virtuális hálózat és egy helyszíni hely között a nyilvános interneten keresztül, használja az [Azure VPN Gateway](../../vpn-gateway/index.yml)-t.

Az alábbiakban az Azure VPN Gateway, az SSL/TLS és a HTTPS használatára vonatkozó ajánlott eljárásokat ismertetjük.

**Ajánlott eljárás**: Biztonságos hozzáférés a helyszínen található több munkaállomásról egy Azure-beli virtuális hálózatba.   
**Részletek**: [Helyek közötti VPN](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)használata.

**Ajánlott eljárás**: Biztonságos hozzáférés a helyszínen található egyes munkaállomásokról egy Azure-beli virtuális hálózatra.   
**Részletek**: [Pont – hely típusú VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create)használata.

**Ajánlott eljárás**: Nagyobb adatkészletek mozgatása dedikált nagysebességű WAN-kapcsolaton keresztül.   
**Részletek**: [ExpressRoute](/azure/expressroute/expressroute-introduction)használata. Ha úgy dönt, hogy a ExpressRoute-t használja, akkor az alkalmazás szintjén is titkosíthatja az adatvédelmet [SSL/TLS](https://support.microsoft.com/kb/257591) vagy más protokollok használatával a védelem hozzáadásához.

**Ajánlott eljárás**: Az Azure Storage-t a Azure Portal keresztül kezelheti.   
**Részletek**: Minden tranzakció HTTPS-kapcsolaton keresztül történik. A [storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) a HTTPS protokollon keresztül is használhatja az [Azure Storage](https://azure.microsoft.com/services/storage/)szolgáltatással való kommunikációhoz.

Azok a szervezetek, amelyek nem védik az adatátviteli adatvédelmet, érzékenyebbek az ember általi támadásokra, a [lehallgatás](https://technet.microsoft.com/library/gg195641.aspx)és [a](https://technet.microsoft.com/library/gg195821.aspx)munkamenet-eltérítésre. Ezeket a támadásokat a bizalmas adatokhoz való hozzáférés első lépéseként lehet megszerezni.

## <a name="secure-email-documents-and-sensitive-data"></a>Biztonságos e-mailek, dokumentumok és bizalmas adatok

A vállalaton kívül megosztott e-maileket, dokumentumokat és bizalmas adatokat szeretne vezérelni és biztonságossá tenni. A [Azure Information Protection](/azure/information-protection/) egy felhőalapú megoldás, amely segít a szervezetnek a dokumentumok és e-mailek besorolását, címkézését és védelemmel való ellátását. Ezt automatikusan elvégezheti a rendszergazdák, akik szabályokat és feltételeket határoznak meg manuálisan a felhasználóktól, vagy egy olyan kombinációt, amelyben a felhasználók javaslatokat kapnak.

A besorolás minden esetben azonosítható, függetlenül attól, hogy hol tárolják az adatok tárolási helyét vagy közösen a megosztást. A címkék vizuális megjelöléseket tartalmaznak, például fejlécet, láblécet vagy vízjelet. A metaadatok a fájlokhoz és az e-mail-fejlécekhez lettek hozzáadva a tiszta szövegben. A tiszta szöveg biztosítja, hogy más szolgáltatások, például az adatvesztés megelőzésére szolgáló megoldások azonosítják a besorolást, és megtehetik a megfelelő lépéseket.

A védelmi technológia az Azure Rights Management (Azure RMS) szolgáltatást használja. Ez a technológia integrálva van más Microsoft Cloud Services és alkalmazások, például az Office 365 és a Azure Active Directory szolgáltatásokkal. Ez a védelmi technológia titkosítási, identitás-és engedélyezési házirendeket használ. A Azure RMS által alkalmazott védelem a dokumentumok és e-mailek, a helytől függetlenül marad – a szervezeten, hálózatokon, fájlkiszolgálókon és alkalmazásokon belül vagy kívül.

Ez az adatvédelmi megoldás megtartja az adatok felügyeletét, még akkor is, ha más személyekkel van megosztva. A Azure RMS is használhatja saját üzletági alkalmazásaival és adatvédelmi megoldásaival a szoftvergyártók számára, függetlenül attól, hogy ezek az alkalmazások és megoldások a helyszínen vagy a felhőben vannak.

Javasoljuk, hogy:

- [Azure Information Protection üzembe helyezése](/azure/information-protection/deployment-roadmap) a szervezet számára.
- Az üzleti igényeknek megfelelő címkéket alkalmazhat. Példa: Alkalmazzon egy "szigorúan bizalmas" címkét minden olyan dokumentumra és e-mailre, amely szigorúan titkos adatot tartalmaz az adatok osztályozásához és védelméhez. Ezt követően csak a jogosult felhasználók férhetnek hozzá ezekhez az értékekhez a megadott korlátozásokkal.
- Konfigurálja a [Azure RMS használati naplózását](/azure/information-protection/log-analyze-usage) , hogy nyomon követheti, hogyan használja a szervezet a védelmi szolgáltatást.

Az adatbesorolásra és [](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) a fájlokra vonatkozó védelem gyenge szervezetei hajlamosabbak az adatszivárgásra vagy az adatvisszaélésre. A megfelelő fájlkezelővel elemezheti az adatfolyamatokat, hogy betekintést nyerjen a vállalatba, azonosítsa a kockázatos viselkedéseket és javítsa a szükséges intézkedéseket, nyomon követheti a dokumentumokhoz való hozzáférést stb.

## <a name="next-steps"></a>További lépések

Az Azure-beli felhőalapú megoldások tervezésekor, üzembe helyezése és kezelése során ajánlott biztonsági eljárásokat az [Azure biztonsági](best-practices-and-patterns.md) eljárásaival és modelljeivel foglalkozó témakörben talál.

Az Azure-biztonsággal és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általános információk az alábbi forrásokból érhetők el:
* Az [Azure Security csapatának blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb frissítéseiről
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – a Microsoft biztonsági rései, például az Azure-nal kapcsolatos problémák, jelentések vagy e-mailen keresztülsecure@microsoft.com
