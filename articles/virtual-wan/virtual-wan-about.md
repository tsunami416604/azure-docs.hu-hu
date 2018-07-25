---
title: Az Azure Virtual WAN áttekintése | Microsoft Docs
description: Ismerkedjen meg a Virtual WAN biztosította automatizált, skálázható, ágak közötti kapcsolódási képességekkel.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/10/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 54cf6c356ec4bb51b123e48c52c5beebc990e59d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009970"
---
# <a name="what-is-azure-virtual-wan-preview"></a>Mi az Azure Virtual WAN? (Előzetes verzió)

Az Azure Virtual WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ágak közötti kapcsolódást biztosít az Azure-on keresztül. A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ez végrehajtható manuálisan vagy a választott szolgáltatói eszközökkel valamelyik Virtual WAN-partneren keresztül. A választott szolgáltatói eszközökkel könnyen használható a rendszer, és egyszerűvé válik a csatlakozás és a konfiguráció kezelése. Az Azure WAN beépített irányítópultjával azonnali hibaelhárítási segítséget kaphat, amelynek köszönhetően időt takaríthat meg, valamint könnyen áttekintheti a nagy kiterjedésű helyek közötti kapcsolatokat.

> [!IMPORTANT]
> Az Azure Virtual WAN jelenleg felügyelt nyilvános előzetes verzióként érhető el. A Virtual WAN használatához [regisztrálnia kell az előzetes verzióra](#enroll).
>
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A cikk gyors betekintést biztosít az Azure-beli és nem Azure-beli számítási feladatok hálózati kapcsolataiba. A Virtual WAN a következő előnyöket biztosítja:

* **Integrált csatlakozási megoldások a küllős topológiákban:** Automatizált helyek közötti konfiguráció és kapcsolódás a helyszíni helyek és egy Azure-elosztó között különféle forrásokból, beleértve a Virtual WAN-partnerek megoldásait is.
* **Automatikus küllőbeállítás és -konfiguráció:** A virtuális hálózatok és a számítási feladatok zökkenőmentes csatlakoztatása az Azure-elosztóhoz.
* **Intuitív hibaelhárítás:** Az Azure-beli, végpontok közötti folyamat megtekinthető, és ezeknek az információknak az alapján megtehetők a szükséges intézkedések.

![A Virtual WAN ábrája](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Együttműködés a Virtual WAN-partnerekkel

1. Az ágeszköz (VPN/SDWAN) vezérlője hitelesítve van, hogy a helyközpontú adatokat az Azure-ba egy Azure-szolgáltatásnéven keresztül exportálja.
2. Az ágeszköz (VPN/SDWAN) vezérlője kéri le az Azure-kapcsolat konfigurációját, és frissíti a helyi eszközt. Ez automatizálja a konfiguráció letöltését, szerkesztését és frissítését a helyszíni VPN-eszközön.
3. Miután az eszköz a megfelelő Azure-konfigurációval rendelkezik, a helyek közötti kapcsolat (két aktív alagút) felépül az Azure WAN irányába. Az Azure használatához az ág (VPN/SDWAN) vezérlőjének támogatnia kell az IKEv2-t. A BGP használata nem kötelező.

## <a name="resources"></a>Virtual WAN-erőforrások

Végpontok közötti virtuális WAN konfigurálásához a következő erőforrásokat kell létrehozni:

* **virtualWAN:** A virtualWAN erőforrás az Azure-hálózat egy virtuális átfedését jelképezi, amely különféle erőforrások gyűjteménye. Hivatkozást tartalmaz az összes virtuális elosztóra, amelyet bele szeretne foglalni a virtuális WAN-hálózatba. A Virtual WAN-erőforrások elkülönülnek egymástól, és nem tartalmazhatnak közös elosztót. A Virtual WAN virtuális elosztói nem kommunikálnak egymással.

* **Hely:** A vpnsite néven is ismert helyerőforrás a helyszíni VPN-eszközt és annak beállításait jelöli. A Virtual WAN-partnerekkel együttműködve olyan beépített megoldásokhoz fér hozzá, amelyekkel automatikusan exportálhatók ezek az adatok az Azure-ba.

* **Elosztó:** A virtuális elosztó egy, a Microsoft által felügyelt virtuális hálózat. Az elosztó különféle szolgáltatásvégpontokat tartalmaz a helyszíni hálózatból (vpnsite) induló kapcsolatok biztosításához. Az elosztó a hálózat központja egy adott régióban. Azure-régiónként csak egy elosztó létezhet. Amikor az Azure Portalon létrehoz egy elosztót, ezzel automatikusan létrehoz egy hozzá tartozó virtuális hálózatot és egy VPN Gateway-átjárót is.

  Az elosztó átjárója nem ugyanaz a virtuális hálózati átjáró, amelyet az ExpressRoute és a VPN Gateway esetében használ. Például a Virtual WAN használata esetén a helyszíni helyről nem közvetlenül a virtuális hálózatára hoz létre helyek közötti kapcsolatot. Ehelyett az elosztóra hozza létre a helyek közötti kapcsolatot. A forgalom minden esetben az elosztó átjáróján halad keresztül. Ez azt jelenti, hogy a virtuális hálózatoknak nincs szükségük saját virtuális hálózati átjáróra. A Virtual WAN segítségével a virtuális hálózatok könnyen méretezhetők a virtuális elosztón és annak átjáróján keresztül. 

* **Elosztó virtuális hálózati kapcsolata:** Az elosztó virtuális hálózati kapcsolata erőforrás az elosztó a virtuális hálózathoz való zökkenőmentes kapcsolódását biztosítja. Jelenleg csak olyan virtuális hálózatokhoz kapcsolódhat, amelyekkel egy elosztórégióban van.

##<a name="enroll"></a>Regisztráció az előzetes verzióra

A Virtual WAN konfigurálása előtt regisztrálnia kell az előfizetését az előzetes verzióban. Máskülönben nem használhatja a Virtual WAN-t a portálon. Ehhez küldjön egy e-mailt a következő címre az előfizetés azonosítójával: <azurevirtualwan@microsoft.com>. Az előfizetés regisztrációja után egy e-mailt fog kapni.

## <a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>Az előzetes verzióval kapcsolatos visszajelzés

Nagya értékeljük a visszajelzését. Amennyiben a Virtual WAN-nal kapcsolatos problémát szeretne bejelenteni, vagy (pozitív vagy negatív) visszajelzést küldene, küldjön egy e-mailt az <azurevirtualwan@microsoft.com> címre. Adja meg a vállalat nevét szögletes zárójelek ([]) között az e-mail tárgymezőjében. Emellett, amennyiben hibát jelent be, adja meg az előfizetése azonosítóját is.

## <a name="next-steps"></a>További lépések

Ha helyek közötti kapcsolatot szeretne létrehozni a Virtual WAN használatával, ezt megteheti egy [Virtual WAN-partneren](https://aka.ms/virtualwan) keresztül vagy manuálisan is. A kapcsolat manuális létrehozásával kapcsolatban lásd [a helyek közötti kapcsolatok a Virtual WAN használatával való létrehozását](virtual-wan-site-to-site-portal.md) ismertető cikket.