---
title: Mentse, és az Azure DevTest Labs szolgáltatásban lemezképek terjesztése |} A Microsoft Docs
description: Ismerje meg, hogy egy egyéni rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: feabd055833e5f0d850138af528cce1da82cae49
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439975"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Egyéni rendszerképek mentse, és több labs terjesztéséhez
Ez a cikk tartalmazza a lépéseket, hogy egyéni lemezképeket menteni a már létrehozott virtuális gépek (VM) biztosít. Azt is bemutatja, hogyan ezek más DevTest Labs szolgáltatásban a szervezet egyéni lemezképek terjesztése.

## <a name="prerequisites"></a>Előfeltételek
A következő elemek már helyén kell lennie:

- A kép Factory Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet.
- Az Azure DevOps-projekt, amellyel automatizálható a rendszerkép gyári.
- Kódu dostupná. a parancsfájlok és a configuration (a példánkban az ugyanabban a DevOps-projekt már említettük, az előző lépésben) tartalmazó adatforrás.
- Az Azure Powershell-feladatok builddefiníció.

Ha szükséges, kövesse lépéseket a [egy lemezkép-előállító Futtatás az Azure DevOps](image-factory-set-up-devops-lab.md) létrehozni, vagy állítsa be ezeket az elemeket. 

## <a name="save-vms-as-generalized-vhds"></a>Virtuális gépek Mentés másként általánosított virtuális merevlemezek
Mentse a meglévő virtuális gépek általánosított virtuális merevlemezeket.  Egy PowerShell-példaszkript menteni a meglévő virtuális gépek általános VHD-ként van. A használatához először adjon hozzá egy másik **Azure PowerShell-lel** a builddefiníció feladatot az alábbi képen látható módon:

![Azure PowerShell-lel lépés hozzáadása](./media/save-distribute-custom-images/powershell-step.png)

Miután az új feladat a listában, válassza ki az elemet, így azt is adja meg az összes az adatokat az alábbi képen látható módon: 

![PowerShell-beállítások](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Általános és speciális egyéni rendszerképek
Az a [az Azure portal](https://portal.azure.com), amikor egy virtuális gépet hoz létre egy egyéni rendszerképet, választhat egy általánosított vagy specializált egyéni rendszerkép.

- **Speciális egyéni lemezkép:** A Sysprep/megszüntetési nem futtatták a számítógépen. Azt jelenti, hogy a kép egy pontos másolatot készít az operációsrendszer-lemez a meglévő virtuális gépen (Pillanatkép).  A ugyanolyan fájlok, alkalmazások, felhasználói fiókok, számítógép neve, és így tovább, is minden jelenik meg, ha ez az egyéni rendszerkép létrehozunk egy új gépet.
- **Egyéni rendszerkép általánosítva:** A Sysprep/megszüntetési futtatták a számítógépen.  Ez a folyamat futtatásakor, eltávolítja a felhasználói fiókok, eltávolítja a számítógép nevét, törli a felhasználó beállításjegyzék-struktúrát, stb. a cél az, hogy a kép általánosítása, így egy másik virtuális gép létrehozásakor testre szabható ki.  Ha Ön egy virtuális gép általánosítása (a sysprep futtatásával), a folyamat az aktuális virtuális gép megsemmisít – többé nem fognak működni.

Ugrás az egyéni lemezképek lemezkép-előállító parancsfájlja menteni fogja az előző lépésben létrehozott virtuális gépek a VHD-k (azonosítja az erőforrást az Azure-beli címke alapján).

## <a name="update-configuration-for-distributing-images"></a>Update-konfigurációján lemezképek terjesztése
A folyamat következő lépése, hogy a lemezkép gyári labor ki az egyéni rendszerképek leküldése bármely más labs, hogy szükség van rájuk. Ez a folyamat alapvető része a **labs.json** konfigurációs fájlt. Ezt a fájlt a annak a **konfigurációs** mappa található lemezkép-előállító.

Két dolgot kulcs labs.json konfigurációs fájlban felsorolt:

- Az előfizetés-azonosító és a labor nevét adott cél tesztkörnyezet egyedileg azonosító.
- Rendszerkép található, érdemes lehet leküldeni a labor létrehozása a konfigurációs legfelső szintű relatív elérési útjai, meghatározott készletét. Megadhatja a teljes mappát (a mappában lévő összes rendszerkép megszerezni) túl.

Íme egy példa labs.json fájlban felsorolt két tanulhat. Ebben az esetben két különböző labs lemezképet terjeszti.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Összeállítási feladat létrehozása
Ugyanazokat a lépéseket a cikkben korábban látott használatával vegyen fel egy további **Azure PowerShell-lel** összeállítási feladat, a build definíciója. Adja meg az adatokat, az alábbi képen látható módon: 

![Lemezképek terjesztéséről feladat létrehozása](./media/save-distribute-custom-images/second-build-task-powershell.png)

A paraméterek a következők: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Ez a feladat található lemezkép-előállító egyéni rendszerképek vesz igénybe, és leküldi a Labs.json fájlban meghatározott bármely labs.

## <a name="queue-the-build"></a>A build várólista
A terjesztési összeállítási feladat befejezése után a várólistára helyezését egy új létrehozást, győződjön meg arról, hogy minden működik. A build sikeres befejezése után az új egyéni lemezképek jelennek meg a cél tesztkörnyezetben, amely a Labs.json konfigurációs fájlban megadott.

## <a name="next-steps"></a>További lépések
A sorozat következő cikkben a lemezkép factory és a egy adatmegőrzési házirend- és karbantartása lépéseit frissítése: [Adatmegőrzési szabály beállításához és karbantartási parancsprogramok futtatása](image-factory-set-retention-policy-cleanup.md).
