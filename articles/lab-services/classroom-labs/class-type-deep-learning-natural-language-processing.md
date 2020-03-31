---
title: Az Azure Lab Services használatával a mélytanulásra összpontosító labor beállítása | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthat be egy labort a shell scriptek Linuxon történő tanításához.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 49ef78ac5a7d58d86583d91bf072f0f3131796fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109393"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Az Azure Lab Services használatával a természetes nyelvi feldolgozás mélytanulására összpontosító labor beállítása
Ez a cikk bemutatja, hogyan állíthat be egy labort, amely a természetes nyelvi feldolgozás (NLP) mélytanulására összpontosít az Azure Lab Services használatával. A természetes nyelvi feldolgozás (NLP) a mesterséges intelligencia (AI) egy formája, amely lehetővé teszi a számítógépek fordítását, beszédfelismerését és más nyelvismereti képességeit.  

NLP-osztályt használó diákok egy Linux virtuális gépet (VM) kapnak, hogy megtanulják, hogyan alkalmazhatnak neurális hálózati algoritmusokat az írott emberi nyelv elemzéséhez használt mélytanulási modellek fejlesztéséhez. 

## <a name="lab-configuration"></a>Labor konfigurációja
A labor beállításához azure-előfizetésre van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. Miután rendelkezik egy Azure-előfizetéssel, létrehozhat egy új laborfiókot az Azure Lab Servicesben, vagy használhat egy meglévő laborfiókot. Az új laborfiók létrehozásáról a következő oktatóanyagban: [Bemutató a Labor-fiók beállításához](tutorial-setup-lab-account.md).
 
A tesztkörnyezet-fiók létrehozása után engedélyezze a következő beállításokat a tesztkörnyezet-fiókban: 

| Laborfiók beállítása | Utasítások |
| ----------- | ------------ |  
| Marketplace-képek | Engedélyezze a Data Science Virtual Machine for Linux (Ubuntu) lemezképet a laborfiókon belüli használatra.  Az utasításokat a következő cikkben [találja: Adja meg a laborkészítők számára elérhető marketplace-képeket.](specify-marketplace-images.md) | 

Kövesse [ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) egy új labor létrehozásához és alkalmazza a következő beállításokat:

| Labor beállításai | Érték/utasítások | 
| ------------ | ------------------ |
| Virtuális gép (VM) mérete | Kis GPU (Számítás). Ez a méret a legalkalmasabb olyan nagy számítási igényű és hálózatigényes alkalmazásokhoz, mint a mesterséges intelligencia és a mélytanulás. |
| Virtuális gép képe | [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Ez a lemezkép mélytanulási keretrendszereket és gépi tanuláshoz és adatelemzéshez szükséges eszközöket biztosít. A lemezképtelepített eszközeinek teljes listájának megtekintéséhez tekintse meg a következő cikket: [Mi található a DSVM-en?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Távoli asztali kapcsolat engedélyezése | Engedélyezi. <p>Ha engedélyezi ezt a beállítást, a tanárok és a diákok a Távoli asztal (RDP) használatával csatlakozhatnak virtuális gépeikhez.</p><p>**Fontos:** Az RDP már telepítve van és konfigurálva van a Data Science virtuális gép Linux rendszerkép. Ennek eredményeképpen a tanárok/diákok további lépések nélkül csatlakozhatnak a virtuális gépekhez az RDP-n keresztül. Továbbá, ha csatlakoznia kell a grafikus asztalhoz, a képmár telepítve van az [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) a virtuális gépen. A diákoknak telepíteniük kell az X2Go klienst a helyi gépeikre, és az ügyfél használatát kell használniuk a csatlakozáshoz. További információt az alábbi útmutatókban talál: <ul><li>[Hogyan lehet hozzáférni a Data Science Virtual Machine for Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Csatlakozás a sablon virtuális gép rdp és GUI csomagok telepítéséhez](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

A Data Science Virtual Machine for Linux rendszerkép biztosítja a szükséges mély tanulási keretrendszerek és eszközök szükségesek az ilyen típusú osztály. Ennek eredményeképpen a sablongép létrehozása után nem kell tovább testreszabnia. Meg lehet közzétenni a diákok használni. A sablon közzétételéhez kattintson **a** közzététel gombra a sablon tesztkörnyezetben való közzétételéhez.  

## <a name="cost"></a>Költségek
Ha meg szeretné becsülni a labor költségét, a következő példát használhatja: 

Egy 25 fős, 20 órás ütemezett óraidőt és 10 órányi kvótát a házi feladatra vagy a feladatokra vonatkozóan a labor ára - 25 diák * (20 + 10) óra * 139 Labor egység * 0,01 USD óránként = 1042,5 USD

További részletek a díjszabásról az [Azure Lab Services díjszabása.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Összegzés
Ez a cikk végigvezet a lépéseket, hogy hozzon létre egy labor természetes nyelvi feldolgozási osztály. Más mélytanulási osztályokhoz is használhat hasonló beállításokat.

## <a name="next-steps"></a>További lépések
A következő lépések gyakoriak a tesztkörnyezet beállításában:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail regisztrációs linkek a diákok](how-to-configure-student-usage.md#send-invitations-to-users). 

