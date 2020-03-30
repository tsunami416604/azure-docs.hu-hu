---
title: Adatbiztonsággal és titkosítással kapcsolatos gyakorlati tanácsok – Microsoft Azure
description: Ez a cikk az Azure-képességek beépített használatával az adatbiztonsággal és titkosítással kapcsolatos gyakorlati tanácsok készletét tartalmazza.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: c5bf62f434b2095f7200b5562c38c252a0195c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243496"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Az Azure adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárásai
Ez a cikk az adatbiztonsággal és -titkosítással kapcsolatos gyakorlati tanácsokat ismerteti.

Az ajánlott eljárások a véleménykonszenzuson alapulnak, és a jelenlegi Azure platform-képességekkel és szolgáltatáskészletekkel működnek. A vélemények és a technológiák idővel változnak, és ezt a cikket rendszeresen frissítik, hogy tükrözze ezeket a változásokat.

## <a name="protect-data"></a>Adatok védelme
A felhőben lévő adatok védelme érdekében figyelembe kell vennie azokat a lehetséges állapotokat, amelyekben az adatok előfordulhatnak, és milyen vezérlők érhetők el az adott állapothoz. Az Azure-adatok biztonsága és titkosítása ajánlott eljárások a következő adatállapotokra vonatkoznak:

- Nyugalmi állapotban: Ez magában foglalja az összes olyan információtároló objektumot, tárolót és típust, amely statikusan létezik a fizikai adathordozón, legyen az mágneses vagy optikai lemez.
- Átvitel közben: Amikor az adatok átvitele összetevők, helyek vagy programok között történik, akkor az átvitel alatt van. Ilyenek például a hálózaton keresztül, egy szolgáltatásbuszon keresztül (a helyszíni felhőbe és fordítva, beleértve a hibrid kapcsolatok, például ExpressRoute), vagy egy bemeneti/kimeneti folyamat során.

## <a name="choose-a-key-management-solution"></a>Válasszon kulcskezelési megoldást

A kulcsok védelme elengedhetetlen a felhőben lévő adatok védelméhez.

Az [Azure Key Vault](/azure/key-vault/key-vault-overview) segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

A Key Vault segítségével több biztonságos tárolót, úgynevezett tárolókat hozhat létre. Ezeket a páncéltermeket HSM-ek támasztják alá. A tárolók a titkos alkalmazáskulcsok tárolásának központosításával csökkentik a biztonsági információk véletlen elvesztésének kockázatát. A kulcstartók a bennük tárolt tartalomhoz való hozzáférést vezérlik és naplózzák is. Az Azure Key Vault képes kezelni a Transport Layer Security (TLS) tanúsítványok igénylését és megújítását. A tanúsítványéletciklus-kezelés robusztus megoldásának szolgáltatásait biztosítja.

Az Azure Key Vault alkalmazáskulcsok és titkos kulcsok támogatására lett tervezve. A Key Vault nem felhasználói jelszavak tárolására szolgál.

Az alábbiakban a Key Vault használatával kapcsolatos biztonsági gyakorlati tanácsokat tik.

**Ajánlott eljárás:** Hozzáférés megadása felhasználóknak, csoportoknak és alkalmazásoknak egy adott hatókörben.   
**Részlet:** Használja az RBAC előre definiált szerepköreit. Például hozzáférést biztosít egy felhasználó nak a kulcstartók kezeléséhez, hozzá kell rendelnie az előre definiált [szerepkör key vault közreműködője](/azure/role-based-access-control/built-in-roles) a felhasználó egy adott hatókörben. A hatókör ebben az esetben egy előfizetés, egy erőforráscsoport, vagy csak egy adott key vault. Ha az előre definiált szerepkörök nem felelnek meg az igényeinek, [megadhatja a saját szerepköreit.](/azure/role-based-access-control/custom-roles)

**Ajánlott eljárás:** Szabályozhatja, hogy a felhasználók milyen hozzáféréssel rendelkeznek.   
**Részlet:** A kulcstartóhoz való hozzáférés két külön felületen keresztül történik: a felügyeleti sík és az adatsík. A felügyeleti sík és az adatsík hozzáférés-vezérlése egymástól függetlenül működik.

RBAC használatával szabályozhatja, hogy mihez férhetnek hozzá a felhasználók. Ha például egy alkalmazásnak hozzáférést szeretne adni a kulcsok kulcsainak használatához egy key vaultban, csak a kulcstároló hozzáférési szabályzatok használatával kell megadnia az adatsík-hozzáférési engedélyeket, és ehhez az alkalmazáshoz nincs szükség felügyeleti sík-hozzáférésre. Ha viszont azt szeretné, hogy a felhasználó olvasni tudja a tároló tulajdonságait és címkéit, de ne férjen hozzá a kulcsokhoz, titkos kulcsokhoz és tanúsítványokhoz, engedélyezhet ennek a felhasználónak „olvasási” hozzáférést RBAC használatával, de az adatsíkhoz nem szükséges hozzáférést biztosítani.

**Ajánlott eljárás:** Tanúsítványok tárolása a key vaultban. A bizonyítványai nagy értéket képviselnek. Rossz kezekben az alkalmazás biztonsága vagy az adatok biztonsága sérülhet.   
**Részletes:** Az Azure Resource Manager biztonságosan telepítheti az Azure Key Vaultban tárolt tanúsítványokat az Azure virtuális gépekre a virtuális gépek telepítésekor. Ha a kulcstartóhoz a megfelelő hozzáférési szabályzatokat állítja be, azt is szabályozhatja, hogy ki férhet hozzá a tanúsítványhoz. Egy másik előnye, hogy az összes tanúsítvány egy helyen kezelhető az Azure Key Vaultban. További [információ: Tanúsítványok telepítése az ügyfél által felügyelt key vaultból származó virtuális gépekre.](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)

**Ajánlott eljárás:** Győződjön meg arról, hogy a kulcstartók vagy a key vault objektumok törlését helyre tudja állítani.   
**Részlet:** A kulcstartók vagy a key vault-objektumok törlése lehet véletlen vagy rosszindulatú. Engedélyezze a Key Vault visszaállítható törlés funkcióját és a végleges törlés elleni védelmet, elsősorban az olyan kulcsokhoz, amelyek az inaktív adatok védelmére vannak használva. Az ilyen kulcsok törlése az adatvesztéssel egyenértékű, ezért fontos, hogy a törölt kulcstartókat és kulcstartó-objektumokat szükség esetén vissza tudja állítani. Gyakorold a Key Vault helyreállítási műveletek rendszeres en.

> [!NOTE]
> Ha a kulcstartó felügyeleti síkjához a felhasználónak közreműködői engedélye (RBAC) van, hozzáférést engedélyezhet magának az adatsíkhoz is a kulcstartó-hozzáférési szabályzat beállításával. Azt javasoljuk, hogy szorosan szabályozhatja, hogy ki rendelkezik közreműködői hozzáféréssel a kulcstartókhoz, hogy csak arra jogosult személyek férhessenek hozzá és kezelhetjék a kulcstartókat, kulcsokat, titkos kulcsokat és tanúsítványokat.
>
>

## <a name="manage-with-secure-workstations"></a>Kezelés biztonságos munkaállomásokkal

> [!NOTE]
> Az előfizetés rendszergazdájának vagy tulajdonosának biztonságos hozzáférésű munkaállomást vagy kiemelt hozzáférésű munkaállomást kell használnia.
>
>

Mivel a támadások túlnyomó többsége a végfelhasználót célozza meg, a végpont lesz az egyik elsődleges támadási pont. A végpontot veszélyeztető támadó a felhasználó hitelesítő adataival férhet hozzá a szervezet adataihoz. A legtöbb végponttámadás kihasználja azt a tényt, hogy a felhasználók a helyi munkaállomások rendszergazdái.

**Ajánlott eljárás:** Használjon biztonságos felügyeleti munkaállomást a bizalmas fiókok, feladatok és adatok védelmére.   
**Részlet:** Használjon [kiemelt hozzáférésű munkaállomást](https://technet.microsoft.com/library/mt634654.aspx) a munkaállomások támadási felületének csökkentéséhez. Ezek a biztonságos felügyeleti munkaállomások segíthetnek a támadások csökkentésében, és biztosíthatják az adatok biztonságát.

**Ajánlott eljárás:** Végpontvédelem biztosítása.   
**Részletes:** Biztonsági házirendek kényszerítése az összes olyan eszközön, amely az adatok felhasználására szolgál, függetlenül az adatok helyétől (felhőben vagy a helyszínen).

## <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

[Az inaktív adatok titkosítása](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) kötelező lépés az adatvédelem, a megfelelőség és az adatok szuverenitása felé.

**Ajánlott eljárás:** Az adatok védelme érdekében alkalmazzon lemeztitkosítást.   
**Részletek**: Használja [az Azure lemeztitkosítást.](/azure/security/azure-security-disk-encryption-overview) Lehetővé teszi, hogy a rendszergazdák titkosítsák a Windows és linuxos IaaS virtuálisgép-lemezeket. A Lemeztitkosítás egyesíti az iparági szabványnak megfelelő Windows BitLocker szolgáltatást és a Linux dm-crypt funkciót, hogy kötettitkosítást biztosítson az operációs rendszer és az adatlemezek számára.

Az Azure Storage és az Azure SQL Database alapértelmezés szerint titkosítja az inaktív adatokat, és számos szolgáltatás kínál titkosítást opcióként. Az Azure Key Vault használatával szabályozhatja az adatokat elérő és titkosító kulcsokat. [További információ: Az Azure-erőforrás-szolgáltatók titkosítási modellének támogatása további információkért tekintse meg.](encryption-atrest.md#azure-resource-providers-encryption-model-support)

**Gyakorlati tanácsok:** A titkosítás segítségével csökkentheti a jogosulatlan adathozzáféréssel kapcsolatos kockázatokat.   
**Részletes:** Titkosítsa a meghajtókat, mielőtt bizalmas adatokat írna rájuk.

Az adattitkosítást nem kikényszerítő szervezetek jobban ki vannak téve az adatok titkosságának. Előfordulhat például, hogy illetéktelen vagy szabályszegő felhasználók adatokat lopnak a feltört fiókokban, vagy jogosulatlanul hozzáférnek a Clear Format formátumban kódolt adatokhoz. A vállalatoknak azt is bizonyítaniuk kell, hogy szorgalmasak és megfelelő biztonsági ellenőrzéseket alkalmaznak adatbiztonságuk növelése érdekében, hogy megfeleljenek az iparági előírásoknak.

## <a name="protect-data-in-transit"></a>Az átvitel alatt álló adatok védelme

Az átvitel alatt álló adatok védelmének fontos szerepet kell kapnia az adatvédelmi stratégiában. Mivel az adatok oda-vissza mozognak számos hely között, általában azt javasoljuk, hogy mindig használjon SSL/TLS protokollt a különböző helyek közötti adatcserékhez. Bizonyos körülmények között érdemes lehet a helyszíni és a felhőbeli infrastruktúra közötti teljes kommunikációs csatornát elkülöníteni egy VPN használatával.

A helyszíni infrastruktúra és az Azure között mozgó adatok esetében célszerű megfontolni a megfelelő védelmi lehetőségek, például a HTTPS vagy a VPN használatát. Ha titkosított forgalmat küld egy Azure virtuális hálózat és egy helyszíni hely között a nyilvános interneten keresztül, használja az [Azure VPN Gateway-t.](../../vpn-gateway/index.yml)

Az alábbiakban az Azure VPN Gateway, az SSL/TLS és a HTTPS használatával kapcsolatos gyakorlati tanácsok találhatók.

**Ajánlott eljárás:** Biztonságos hozzáférés több, a helyszínen található munkaállomásokról egy Azure virtuális hálózathoz.   
**Részletek**: Használja [a helyek közötti VPN-t](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Ajánlott eljárás:** Biztonságos hozzáférés a helyszínen található egyes munkaállomásokról egy Azure virtuális hálózathoz.   
**Részlet**: Használja [a pont-hely VPN-t](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Ajánlott eljárás:** Nagyobb adatkészletek áthelyezése dedikált nagy sebességű WAN-kapcsolaton keresztül.   
**Részlet**: Használja [az ExpressRoute](/azure/expressroute/expressroute-introduction)-t . Ha az ExpressRoute használata mellett dönt, az adatokat az alkalmazás szintjén is titkosíthatja az SSL/TLS vagy más protokoll használatával, így fokozhatja a védelmet.

**Ajánlott eljárás:** Az Azure Storage-szal az Azure Portalon keresztül.   
**Részlet**: Minden tranzakció HTTPS-en keresztül történik. A Storage [REST API HTTPS-en](https://msdn.microsoft.com/library/azure/dd179355.aspx) keresztüli használatával is kommunikálhat az [Azure Storage szolgáltatással.](https://azure.microsoft.com/services/storage/)

Azok a szervezetek, amelyek nem védik az adatokat az átvitel során, hajlamosabbak [a köztes műveletekre, a](https://technet.microsoft.com/library/gg195821.aspx) [lehallgatásra](https://technet.microsoft.com/library/gg195641.aspx)és a munkamenet-eltérítésre. Ezek a támadások a bizalmas adatokhoz való hozzáférés első lépései lehetnek.

## <a name="secure-email-documents-and-sensitive-data"></a>Biztonságos e-mailek, dokumentumok és bizalmas adatok

Szabályozni és biztonságossá szeretné tenni a vállalaton kívül megosztott e-maileket, dokumentumokat és bizalmas adatokat. Az [Azure Information Protection](/azure/information-protection/) olyan felhőalapú megoldás, amely segítségével a szervezetek osztályozhatják, címkézhetik és védelemmel láthatják el a dokumentumaikat és az e-mailjeiket. Ezt automatikusan elvégezhetik a rendszergazdák, akik szabályokat és feltételeket határoznak meg, manuálisan a felhasználók által, vagy olyan kombináció, ahol a felhasználók javaslatokat kapnak.

A besorolás mindenkor azonosítható, függetlenül attól, hogy hol tárolják az adatokat, vagy kivel vannak megosztva. A címkékhez vizuális jelölések (például fejlécek, láblécek vagy vízjelek) is tartoznak. A rendszer tiszta szöveges formátumban adja hozzá a metaadatokat a fájlokhoz és e-mail-fejlécekhez. A világos szöveg biztosítja, hogy más szolgáltatások, például az adatvesztés megelőzésére szolgáló megoldások azonosíthassák a besorolást, és megtehessék a megfelelő lépéseket.

A védelmi technológia az Azure Rights Management (Azure RMS) szolgáltatást használja. Ez a technológia más Microsoft-felhőszolgáltatásokba és -alkalmazásokba (például Office 365 és Azure Active Directory). A védelmi technológia titkosítási, identitáskezelési és engedélyezési házirendeket használ. Az Azure RMS-en keresztül alkalmazott védelem a dokumentumok és az e-mailek között marad, függetlenül a helyétől – a szervezeten, a hálózatokon, a fájlkiszolgálókon és az alkalmazásokon belül vagy kívül.

Ez az információvédelmi megoldás akkor is kézben tarthatja az adatokat, ha azokat másokkal is megosztja. Az Azure RMS-t saját üzletági alkalmazásokkal és szoftvergyártók tól származó információvédelmi megoldásokkal is használhatja, függetlenül attól, hogy ezek az alkalmazások és megoldások helyszíni vagy felhőbeli.

A következő megoldást javasoljuk:

- [Telepítse az Azure Information Protection-t](/azure/information-protection/deployment-roadmap) a szervezetszámára.
- Az üzleti igényeket tükröző címkék alkalmazása. Például: Alkalmazzon egy "szigorúan bizalmas" nevű címkét minden olyan dokumentumra és e-mailre, amely szigorúan titkos adatokat tartalmaz, hogy osztályozza és megvédje ezeket az adatokat. Ezután csak a jogosult felhasználók férhetnek hozzá ezekhez az adatokhoz, a megadott korlátozásokkal.
- Konfigurálja [az Azure RMS használati naplózását,](/azure/information-protection/log-analyze-usage) hogy figyelhesse, hogyan használja a szervezet a védelmi szolgáltatást.

Az [adatbesorolás](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és a fájlvédelem terén gyengék szervezetek érzékenyebbek lehetnek az adatszivárgásra vagy az adatokkal való visszaélésre. A megfelelő fájlvédelemmel elemezheti az adatfolyamokat, hogy betekintést nyerjen a vállalkozásába, észlelhesse a kockázatos viselkedést, és korrekciós intézkedéseket tegyen, nyomon követheti a dokumentumokhoz való hozzáférést, és így tovább.

## <a name="next-steps"></a>További lépések

Tekintse meg [az Azure biztonsági gyakorlati tanácsait és mintáit,](best-practices-and-patterns.md) amelyek további biztonsági gyakorlati tanácsokat tartalmaznak a felhőbeli megoldások tervezése, üzembe helyezése és kezelése során az Azure használatával.

Az alábbi források az Azure biztonságával és a kapcsolódó Microsoft-szolgáltatásokkal kapcsolatos általánosabb információk biztosításához érhetők el:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) – naprakész információk az Azure Security legújabb adatairól
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – ahol a Microsoft biztonsági rései, beleértve az Azure-ral kapcsolatos problémákat is, jelenthetők, vagy e-mailben asecure@microsoft.com
