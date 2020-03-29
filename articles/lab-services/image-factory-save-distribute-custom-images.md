---
title: Lemezképek mentése és terjesztése az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ez a cikk ismerteti az egyéni lemezképek mentésének lépéseit az Azure DevTest Labs már létrehozott virtuális gépekről (VM-ek).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: e5bc8e5041bfe6d95e3ff1a93bb3338ccead5bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759431"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Egyéni rendszerképek mentése és továbbítása több tesztkörnyezetbe
Ez a cikk a már létrehozott virtuális gépek (VM-ek) egyéni lemezképeinek mentéséhez szükséges lépéseket ismerteti. Azt is ismerteti, hogyan terjesztheti ezeket az egyéni rendszerképeket a szervezet más DevTest Labs.

## <a name="prerequisites"></a>Előfeltételek
A következő elemeknek már a helyükön kell lenniük:

- Az Azure DevTest Labs image factory tesztkörnyezete.
- Egy Azure DevOps-projekt, amely a lemezkép-gyár automatizálására szolgál.
- A parancsfájlokat és a konfigurációt tartalmazó forráskód helye (példánkban az előző lépésben említett ugyanabban a DevOps-projektben).
- Az Azure Powershell-feladatok vezényléséhez hozzon létre definíciót.

Szükség esetén kövesse a [Lemezkép-gyár futtatása az Azure DevOps-ból](image-factory-set-up-devops-lab.md) lépéseket ezeknek az elemeknek a létrehozásához vagy beállításához. 

## <a name="save-vms-as-generalized-vhds"></a>Virtuális gépek mentése általános virtuális gépként
Mentse a meglévő virtuális gépekáltalános virtuális gépek.  Van egy powershell-parancsfájl, amely általános virtuális gépekként menti a meglévő virtuális gépeket. A használathoz először adjon hozzá egy másik **Azure Powershell-feladatot** a builddefinícióhoz az alábbi képen látható módon:

![Azure PowerShell-lépés hozzáadása](./media/save-distribute-custom-images/powershell-step.png)

Miután az új feladat a listában, válassza ki az elemet, így tudjuk kitölteni az összes részletet, ahogy az a következő képen látható: 

![PowerShell-beállítások](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Általános és speciális egyéni képek
Az [Azure Portalon,](https://portal.azure.com)amikor egy virtuális gépről egyéni lemezképet hoz létre, választhat, hogy egy általános vagy egy speciális egyéni lemezképet.

- **Speciális egyéni kép:** A Sysprep/Deprovision nem futott a számítógépen. Ez azt jelenti, hogy a lemez az operációs rendszer lemezének pontos másolata a meglévő virtuális gépen (pillanatkép).  Ugyanazok a fájlok, alkalmazások, felhasználói fiókok, számítógépnév és így tovább, mind jelen vannak, amikor új gépet hozunk létre erről az egyéni lemezképről.
- **Általános egyéni lemezkép:** A Sysprep/Deprovision futtatása a számítógépen történt.  Amikor ez a folyamat fut, eltávolítja a felhasználói fiókokat, eltávolítja a számítógép nevét, eltávolítja a felhasználói rendszerleíró struktúrákat stb., azzal a céllal, hogy általánosítsa a lemezképet, így testre szabható egy másik virtuális gép létrehozásakor.  Amikor általánosít egy virtuális gépet (a sysprep futtatásával), a folyamat elpusztítja az aktuális virtuális gépet – a továbbiakban nem lesz működőképes.

A parancsfájl az egyéni lemezképek dokkolása a rendszerképgyárban menti a virtuális gépek az előző lépésben létrehozott (az azure-beli erőforrás címkéje alapján azonosított) virtuális gépek mentését.

## <a name="update-configuration-for-distributing-images"></a>Lemezképek terjesztésének konfigurációjának frissítése
A következő lépés a folyamat, hogy leküldéses az egyéni képeket a képgyári labor ki bármely más laborok, amelyek nek szükségük van rájuk. A folyamat központi része a **labs.json konfigurációs** fájl. Ezt a fájlt a **lemezképgyárkonfigurációs** mappájában találja.

A labs.json konfigurációs fájlban két kulcsfontosságú dolog található:

- Egyedileg azonosítja egy adott céllabor az előfizetés-azonosító és a labor neve használatával.
- A konfigurációs gyökér relatív elérési útjaként a laborba lekell nyomni a rendszerképek adott készletét. Megadhatja a teljes mappát (az összes kép bekerüléséhez a mappában) is.

Íme egy példa labs.json fájl két labs felsorolt. Ebben az esetben képeket terjeszt két különböző laborban.

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
A cikkben korábban ismertetett lépések használatával adjon hozzá egy további **Azure Powershell-buildfeladatot** a builddefinícióhoz. Töltse ki a részleteket az alábbi képen látható módon: 

![Képek terjesztésére irányuló feladat létrehozása](./media/save-distribute-custom-images/second-build-task-powershell.png)

A paraméterek a következők:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Ez a feladat a lemezképgyárban lévő egyéni lemezképeket a Labs.json fájlban definiált bármely laborba kilöki.

## <a name="queue-the-build"></a>A build várólistára állítása
Miután a terjesztési build feladat befejeződött, várólistára egy új build, hogy megbizonyosodjon arról, hogy minden működik. Miután a build sikeresen befejeződött, az új egyéni lemezképek megjelennek a labs.json konfigurációs fájlba beírt céllaborban.

## <a name="next-steps"></a>További lépések
A sorozat következő cikkében adatmegőrzési szabályzattal és törlési lépésekkel frissíti a [lemezképgyárat: Adatmegőrzési házirend beállítása és törlési parancsfájlok futtatása.](image-factory-set-retention-policy-cleanup.md)
