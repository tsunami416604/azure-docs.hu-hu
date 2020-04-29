---
title: Hozzáférés & Application Controls oktatóanyaghoz – Azure Security Center
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy igény szerinti virtuálisgép-hozzáférési szabályzatot és egy alkalmazás-ellenőrzési házirendet.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 0b28de7af16053093cd0108224188cdd615fce55
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80435522"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Oktatóanyag: Erőforrások védelme az Azure Security Centerrel
A Security Center korlátozza a fenyegetéseknek való kitettségét azzal, hogy hozzáférés- és alkalmazásvezérlőket használ a kártékony tevékenységek blokkolására. Az igény szerinti (JIT) virtuálisgép-hozzáférés csökkenti a támadásoknak való kitettséget azáltal, hogy lehetővé teszi a virtuális gépek állandó hozzáférésének megtagadását. Az állandó hozzáférés helyett szabályozott és naplózott hozzáférést biztosít a virtuális gépekhez – csak akkor, ha szükség van rá. Az adaptív alkalmazásvezérlők segítenek felvértezni a virtuális gépeket a kártevők ellen azáltal, hogy szabályozzák, mely alkalmazások futhatnak rajtuk. A Security Center gépi tanulási módszerekkel elemzi a virtuális gépen futó folyamatokat, és az így szerzett információk alapján segít az engedélyezési szabályok alkalmazásában.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Igény szerinti virtuálisgép-hozzáférési szabályzat konfigurálása
> * Alkalmazásvezérlési szabályzat konfigurálása

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban ismertetett funkciók végrehajtásához a Security Center Standard tarifacsomagjával kell rendelkeznie. Security Center Standard díjmentesen is kipróbálható. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](security-center-get-started.md) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

## <a name="manage-vm-access"></a>Virtuális gépekhez való hozzáférés kezelése
A JIT VM-hozzáférés segítségével zárolhatja az Azure-beli virtuális gépek bejövő forgalmát, így csökkentve a támadásoknak való kitettséget, miközben könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges.

A felügyeleti portoknak nem kell mindig nyitva lenniük. Csak addig kell nyitva lenniük, amíg Ön csatlakozik a virtuális géphez, például azért, hogy felügyeleti vagy karbantartási feladatokat végezzen. Ha az igény szerinti hozzáférés engedélyezve van, Security Center a hálózati biztonsági csoport (NSG) szabályait használja, amelyek korlátozzák a felügyeleti portok elérését, hogy a támadók ne tudják megcélozni azokat.

1. A Security Center főmenüjében válassza az igény szerinti virtuális gépekhez **való hozzáférést** a **speciális Felhőbeli védelem**területen.

   ![Virtuális gépek igény szerinti elérése][1]

   Az igény szerinti virtuálisgép- **hozzáférés** információt nyújt a virtuális gépek állapotáról:

   - **Konfigurálva** – a virtuális gépek igény szerinti elérésének támogatásához konfigurált virtuális gépek.
   - **Ajánlott** – a virtuális gépek igény szerinti elérését támogató virtuális gépek, de nem lettek konfigurálva a szolgáltatáshoz.
   - **No recommendation** (Nincs javaslat) – A virtuális gépek a következő okokból kerülhetnek ebbe a kategóriába:

     - Hiányzó NSG – az igény szerinti megoldáshoz szükség van egy NSG.
     - Klasszikus virtuális gép – Security Center igény szerinti virtuálisgép-hozzáférés jelenleg csak a Azure Resource Manager használatával telepített virtuális gépeket támogatja.
     - Egyéb – a virtuális gép ebben a kategóriában van, ha az igény szerinti megoldás ki van kapcsolva az előfizetés vagy az erőforráscsoport biztonsági házirendjében, vagy ha a virtuális gép hiányzik egy nyilvános IP-cím, és nem rendelkezik NSG.

2. Válassza ki a kívánt virtuális gépet, majd kattintson a **JIT engedélyezése 1 virtuális gépen** az adott virtuális gép igény szerinti házirendjének konfigurálásához:

   A Security Center által javasolt alapértelmezett portok menthetők, vagy hozzáadhat és konfigurálhat egy új portot, amelyen engedélyezni szeretné az igény szerinti megoldást. Ebben az oktatóanyagban adjunk hozzá egy portot az **Add** (Hozzáadás) paranccsal.

   ![Portkonfiguráció hozzáadása][2]

3. Az **Add port configuration** (Portkonfiguráció hozzáadása) területen a következőket kell megadni:

   - A port
   - A protokolltípus
   - Engedélyezett forrás IP-címek – Olyan IP-címtartományok, amelyek jóváhagyott kérelem esetén hozzáférést kaphatnak.
   - Kérelem maximális időtartama – Az a maximális időtartam, ameddig egy port nyitva lehet.

4. A mentéshez kattintson az **OK** gombra.

## <a name="harden-vms-against-malware"></a>Virtuális gépek kártevők elleni felvértezése
Az adaptív alkalmazásvezérlők segítségével meghatározhatja a konfigurált erőforráscsoportokon futtatható alkalmazások csoportját, ami többek között segít felvértezni virtuális gépeit a kártevők ellen. A Security Center gépi tanulási módszerekkel elemzi a virtuális gépen futó folyamatokat, és az így szerzett információk alapján segít az engedélyezési szabályok alkalmazásában.

1. Térjen vissza a Security Center főmenüjébe. Az **ADVANCED CLOUD DEFENSE** (SPECIÁLIS FELHŐVÉDELEM) területen válassza ki az **Adaptive application controls** (Adaptív alkalmazásvezérlők) elemet.

   ![Adaptív alkalmazásvezérlők][3]

   A **Resource groups** (Erőforráscsoportok) szakaszban három lap található:

   - **Konfigurálva**: az alkalmazás-vezérlővel konfigurált virtuális gépeket tartalmazó erőforráscsoport-csoportok listája.
   - **Ajánlott**: azon erőforráscsoportok listája, amelyekhez javasolt az alkalmazás-vezérlés.
   - **Nincs javaslat**: a virtuális gépeket tartalmazó erőforráscsoportok listája alkalmazás-ellenőrzési javaslatok nélkül. például olyanokat, amelyeken az alkalmazások folyamatosan változnak, és még nem értek el egy stabil állapotot.

2. Válassza ki a **Recommended** (Ajánlott) lapot azon erőforráscsoportok listájáért, amelyeken javasolt bevezetni az alkalmazásvezérlést.

   ![Alkalmazásvezérlési javaslatok][4]

3. Jelölje ki az egyik erőforráscsoportot a **Create application control rules** (Alkalmazásvezérlési szabályok létrehozása) lehetőség megnyitásához. A **Select VMs** (Virtuális gépek kiválasztása) felületen tekintse át a javasolt virtuális gépek listáját, és törölje a jelölést azok mellől, amelyeket nem szeretne bevonni az alkalmazásvezérlés hatókörébe. Az **engedélyezési szabályokhoz tartozó folyamatok kiválasztása lapon**tekintse át az ajánlott alkalmazások listáját, és törölje a jelölést, hogy ne legyenek alkalmazva. A lista a következőket tartalmazza:

   - **Name (név**): az alkalmazás teljes elérési útja
   - **Folyamatok**: Hány alkalmazás található az összes elérési úton
   - **Common (gyakori**): az "igen" érték azt jelzi, hogy ezeket a folyamatokat az erőforráscsoport legtöbb virtuális gépe hajtja végre
   - **EXPLOITABLE** (Kihasználható): Egy figyelmeztető ikon jelzi, ha az alkalmazás használatával az esetleges támadók megkerülhetik az alkalmazásengedélyezési rendszert. Érdemes áttekinteni ezeket az alkalmazásokat az engedélyezésük előtt.

4. Ha végzett a kiválasztással, válassza a **Create** (Létrehozás) lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, folytassa a standard szint futtatását és az automatikus kiépítés engedélyezését. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki az előfizetést vagy a szabályzatot, amelyet vissza szeretne állítani Ingyenes állapotba. Megnyílik a **Biztonsági szabályzat** képernyő.
3. A **SZABÁLYZAT ÖSSZETEVŐI** alatt válassza a **Tarifacsomag** lehetőséget.
4. Válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Security Center főmenüre, és válassza a **biztonsági szabályzat**elemet.
2. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést.
3. Az automatikus kiépítés letiltásához a **Biztonsági szabályzat – Adatgyűjtés** területen válassza a **Ki** lehetőséget az **Előkészítés** elemnél.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Log Analytics ügynököt az Azure-beli virtuális gépekről, ahol az ügynököt kiépítték. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan korlátozhatja a fenyegetéseknek való kitettségét a következőkkel:

> [!div class="checklist"]
> * Az igény szerinti virtuálisgép-hozzáférési szabályzat konfigurálása, amely a virtuális gépekhez csak szükség esetén felügyelt és naplózott hozzáférést biztosít
> * Adaptív alkalmazásvezérlési szabályzat konfigurálása, amely szabályozza, mely alkalmazások futhatnak a virtuális gépeken

Folytassa a következő oktatóanyaggal, amely a biztonsági incidensekre való válaszadást ismerteti.

> [!div class="nextstepaction"]
> [Oktatóanyag: Reagálás a biztonsági incidensekre](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
