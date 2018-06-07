---
title: Az Azure verem telepítési hálózati forgalom |} Microsoft Docs
description: Ez a cikk ismerteti, mit várhatnak, Azure verem telepítési hálózati folyamatokkal kapcsolatos.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655109"
---
# <a name="about-deployment-network-traffic"></a>Központi telepítés hálózati forgalommal kapcsolatos
Az ismertetése, hogyan hálózati forgalom Azure verem során telepítési nélkülözhetetlen annak biztosításához a sikeres telepítés. Ez a cikk bemutatja, hogyan várt hálózati forgalom megismerhesse, mit várhatnak, így a telepítési folyamat során.

Az ábrán látható, az összetevők és a kapcsolatok a telepítési folyamat:

![Az Azure verem telepítési hálózati topológia](media/deployment-networking/figure1.png)

> [!NOTE]
> Ez a cikk ismerteti a csatlakoztatott telepítésének követelményei, a más központi telepítési módszerekkel kapcsolatos további tudnivalókért lásd: [Azure verem üzembe helyezési kapcsolat modell](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>A virtuális gép telepítése
Az Azure-verem megoldás tartalmaz egy Azure verem összetevők futtatására használt kiszolgálókat és az életciklus állomás (HLH) nevű további kiszolgáló csoportját. Ez a kiszolgáló telepítéséhez és a megoldás az életciklus kezeléséhez használt, és a központi telepítés virtuális gép (DVM) tároló üzembe helyezése során.

## <a name="deployment-requirements"></a>A telepítési követelményei
Központi telepítés megkezdése előtt, akkor néhány minimális követelményeknek, annak érdekében, központi telepítés sikeresen befejeződött az OEM által érvényesíthető. Az ismertetése, ezek a követelmények segítségével készítse elő a környezetet, és győződjön meg arról, hogy az érvényesítés sikeres, a következők:

-   [Tanúsítványok](azure-stack-pki-certs.md)
-   [Azure-előfizetés](https://azure.microsoft.com/free/?b=17.06)
-   Internetelérés
-   DNS
-   NTP

> [!NOTE]
> Ez a cikk az utolsó három követelményekre összpontosít. Az első két további információkért lásd: a fenti hivatkozások.

## <a name="deployment-network-traffic"></a>Központi telepítés hálózati forgalom
A DVM olyan IP-címet a BMC hálózatról van konfigurálva, és az internet hálózati kapcsolatra van szüksége. Bár nem az összes a BMC-hálózati összetevők szükségesek a külső útválasztás vagy az internethez való hozzáférést, néhány OEM-specifikus összetevőt, a hálózati IP-címek használata után is szükség lehet azt.

A telepítés során a DVM hitelesíti az Azure Active Directory (Azure AD) segítségével az Azure-előfizetésből fiók ellen. Ehhez a DVM internet kell elérnie az adott port és URL-címek listáját. A teljes listáját megtalálhatja a [közzététele végpontok](azure-stack-integrate-endpoints.md) dokumentációját. A DVM felhasznál egy DNS-kiszolgáló külső URL-címek belső összetevői által végzett DNS-kérések továbbításához. A belső DNS-ben a DNS-továbbító címét, majd való telepítését megelőzően OEM ezeket a kérelmeket továbbítja. Ugyanez érvényes a NTP-kiszolgáló, a pontos idő kiszolgáló vezet az összetevők Azure verem konzisztencia és az idő szinkronizálása.

A telepítés során a DVM által igényelt Internet-hozzáférés kimenő csak, a bejövő hívások nem történik a telepítés során. Ne feledje, hogy az IP azt használja forrásként, és adott Azure verem nem támogatja a proxybeállításokkal. Ezért ha szükséges, akkor kell biztosítania a transzparens proxy vagy NAT az internet eléréséhez. Központi telepítés befejezése után az Azure és az Azure-verem közötti kommunikáció válnak, a külső hálózaton keresztül nyilvános virtuális IP-címek használatával.

Hálózati konfigurációk Azure verem kapcsolókra tartalmazhat-vezérlési listák (ACL), amelyek korlátozzák a forgalmat bizonyos hálózati források és a célhelyek között. A DVM összetevő a csak a korlátlan hozzáféréshez; még a HLH nagyon korlátozódik. Az OEM megkérheti megkönnyítése érdekében a felügyeleti és a hálózati hozzáférés testreszabása beállításokkal kapcsolatban. A hozzáférés-vezérlési listák miatt fontos a központi telepítéskor a DNS- és NTP-kiszolgáló címei megváltoztatásának elkerülése érdekében. Ha így tesz, akkor újra kell konfigurálnia az összes a kapcsolót a megoldáshoz.

Központi telepítés befejezése után a megadott DNS- és NTP-kiszolgálócímek tovább használni a rendszer összetevőit közvetlenül. Például ha DNS-kérelmek központi telepítés befejezése után, a forrás fogja módosítani a DVM IP-címről egy címet a külső hálózati tartományból.

Azure verem sikeres telepítése után a Számítógépgyártó partner használhatja a DVM további telepítés utáni feladatok. Azonban minden telepítési feladatokat és központi telepítés utáni konfigurációk befejezése után az eredeti Berendezésgyártók kell eltávolítása és a DVM törlése a HLH.

## <a name="next-steps"></a>További lépések
[Az Azure regisztráció érvényesítése](azure-stack-validate-registration.md)
