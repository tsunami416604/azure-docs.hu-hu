---
title: Azure Lab Services a Microsoft Teams szolgáltatáson belül
description: Áttekintést nyújt Azure Lab Services használatáról a Microsoft Teams szolgáltatásban.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 8b70ab3cbf1a342646f6a2897550c4bf8f7601a7
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946667"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Azure Lab Services a Microsoft Teams szolgáltatáson belül

Azure Lab Services tulajdonosai/alkotói már létrehozhatnak laborokat a Microsoft Teams szolgáltatásban.

Ez a cikk a csapatok integrációjának előnyeit mutatja be, és hivatkozásokat tartalmaz más cikkekhez, amelyek útmutatást nyújtanak a csapatokon belüli Azure Lab Services kezeléséhez. 

## <a name="benefits"></a>Előnyök

Azure Lab Services a Microsoft Teams integrációja segít a pedagógusok számára a tantermi környezet kialakításában és a csapaton belüli virtuális labor-környezetekben (osztály): 

* Az oktatók úgy állíthatják be a tantermi laborokat, hogy a tanulók a csapatoktól a csapatoktól elhagyva a csapattól és a [Azure Lab Services webhelyének](https://labs.azure.com)elérésével hozzáférjenek a virtuális gépekhez.
* Egyszeri bejelentkezés (SSO) a csapatokból a Azure Lab Servicesba.
* A csapat és a labor tulajdonosai számára nem szükséges, hogy az osztályokat két különböző rendszeren is meg lehessen őrizni – a labor felhasználói listán automatikusan ki van töltve a csoport tagsága, és a szinkronizálást minden 24 órában automatikusan elvégzi a rendszer. 
* A sablon virtuális gép kezdeti közzétételét követően a labor kapacitása (azaz a laborban lévő virtuális gépek száma) automatikusan ki lesz igazítva a csoport tagságának felhasználói hozzáadásával/törlésével. 
* A csapat és a labor tulajdonosai csak a csapattal és diákokkal kapcsolatos laborokat fogják megtekinteni, és csak azokat a virtuális gépeket tekintik meg, amelyek az adott csapat számára lettek kiépítve. 
* A rendszer automatikusan regisztrálja a felhasználókat a laborban, és a rendszer automatikusan hozzárendeli a virtuális gépeket az első bejelentkezéshez a tesztkörnyezet közzétételekor. Az oktatóknak nem kell elküldeniük a meghívókat és a tanulókat nem kell külön regisztrálni a laborba.  

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [Első lépések és labor Services-tesztkörnyezet létrehozása a csapatokból](how-to-get-started-create-lab-within-teams.md)
- [A labor Services felhasználói listájainak kezelése a csapatokból](how-to-manage-user-lists-within-teams.md)
- [Virtuálisgép-készlet kezelése a labor Servicesben a csapatoktól](how-to-manage-vm-pool-within-teams.md)
- [Labor Services-ütemtervek létrehozása a csapatokból](how-to-create-schedules-within-teams.md)
- [Hozzáférés egy virtuális géphez (Student View) a labor Servicesben a csapatoktól](how-to-access-vm-for-students-within-teams.md)
