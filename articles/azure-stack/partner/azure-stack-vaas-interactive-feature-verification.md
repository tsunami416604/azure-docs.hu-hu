---
title: Interaktív szolgáltatás ellenőrző tesztelése az Azure Stack érvényesítési szolgáltatásként |} A Microsoft Docs
description: Ismerje meg, interaktív funkció ellenőrző tesztek létrehozása az Azure Stack-érvényesítéssel szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a483a8e5e4c37c51c4efce13ad69b4abc83a402d
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105372"
---
# <a name="interactive-feature-verification-testing"></a>Interaktív funkció ellenőrzési tesztelés  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Az interaktív funkció ellenőrzési tesztelés keretrendszer használatával tesztek kérése a rendszerhez. Amikor egy vizsgálatot igényel, a Microsoft a keretrendszer készíti elő az interaktív manuális lépéseket igénylő tesztek használja. A Microsoft a keretrendszer használatával lánc együttesen több különálló automatizált teszteket.

Ez a cikk ismerteti egy egyszerű manuális forgatókönyvet. A teszt ellenőrzi, hogy az Azure Stackben lemez cseréje. A keretrendszer összegyűjti az egyes lépéseknél a diagnosztikai naplók. Kapcsolatos problémák megoldásában is, ahogy látja őket. A keretrendszer is lehetővé teszi, hogy a más eszközök vagy a folyamatok által előállított naplók megosztása, és lehetővé teszi a forgatókönyvet a visszajelzést.

## <a name="overview-of-a-test-pass"></a>A tesztelési fázisban áttekintése

Egy teszt lemezcsere egy gyakori forgatókönyvet. Ebben a példában a teszt hét lépésből áll:

1.  Hozzon létre egy új **Tesztmenetek** munkafolyamat.
2.  Válassza ki a **lemez azonosítása teszt**.
3.  A teszt elindításához.
4.  Válassza ki a műveletek az interaktív ellenőrzési forgatókönyv.
5.  Ellenőrizze a forgatókönyv eredményét.
6.  A teszt eredménye küldeni a Microsoftnak.
7.  Ellenőrizze az állapotát a VaaS portálon.

## <a name="create-a-new-test-pass"></a>Hozzon létre egy új tesztelési fázis

1.  Keresse meg a [Azure Stack érvényesítési portál](https://www.azurestackvalidation.com) , és jelentkezzen be.

2.  Hozzon létre egy új megoldást, vagy válasszon ki egy meglévőt.

3.  Válassza ki **Start** a a **Tesztmenetek** csempére.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Adjon meg egy nevet a **Tesztmenetek** munkafolyamat.

5.  Adja meg a környezet és a közös tesztparaméterekre utasításait követve a [munkafolyamat általános paramétereit az Azure Stack érvényesítési szolgáltatásként](azure-stack-vaas-parameters.md) cikk.

6.  A diagnosztika kapcsolati karakterlánc címben megadott formátumnak kell követnie a [teszt paramétereiben](azure-stack-vaas-parameters.md#test-parameters) című rész a [általános munkafolyamat-paraméterek](azure-stack-vaas-parameters.md) cikk.

7.  Adja meg a szükséges paramétereket a vizsgálatot.

8.  Válassza ki az ügynököt, hogy az interaktív vizsgálat futtatása.

> [!Note]  
> Lemez azonosítása interaktív funkció ellenőrző teszt kötelező a tartományi rendszergazdai felhasználónevet és jelszót.

![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Válassza ki a teszt

1.  Válassza ki **lemez azonosítása teszt\<verzió >**.

    > [!Note]  
    > A teszt verzióját, a teszt biztosítékot való fejlesztést növeli. A legújabb verziót mindig kell használni, kivéve, ha a Microsoft egyéb jelzi.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Adja meg a tartomány rendszergazdai felhasználónév és jelszó kiválasztásával **szerkesztése**.

3.  Válassza ki a megfelelő vizsgálati végrehajtási ügynök/DVM meg a teszt elindításához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Válassza ki **küldés** a teszt elindításához.

![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>A Teszt indítása

A lemez azonosítása teszt utasításokat a VaaS ügynököt futtató számítógép megjelenítése. Általában ez az a DVM vagy a Jumpboxot az Azure Stack-példány.

![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Válassza ki a műveleteket

1.  Kövesse a **dokumentáció** és **érvényesítési** hivatkozásokra kattintva útmutatást nyújt a Microsoft a forgatókönyv végrehajtásához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Kattintson a **Tovább** gombra.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Kövesse az utasításokat a precheck parancsfájl futtatásához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  A precheck szkript sikeres befejezése után futtassa a manuális forgatókönyv (lemezcsere) megfelelően a **dokumentáció** és **érvényesítési** származó hivatkozások a **információk**fülre.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  A manuális forgatókönyv működés során ne zárja be a párbeszédpanelt.

6.  Amikor végzett, a manuális forgatókönyv végrehajtása, kövesse az utasításokat a jelölőnégyzet utólagos parancsfájl futtatásához.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  A sikeres végrehajtása manuális forgatókönyv (lemezcsere), jelölje be **tovább**.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Ha bezárja az ablakot, a teszt leáll, mielőtt befejeződött.

## <a name="check-the-status"></a>Az állapot ellenőrzése

1.  A teszt befejeződése után a rendszer kéri, hogy visszajelzést.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Ezeket a kérdéseket a segítségével a Microsoft értékeli a forgatókönyv sikerességi arányát és a kiadási minőségét.

## <a name="send-the-test-result"></a>A vizsgálati eredmények küldése

1.  Csatlakoztassa a Microsoftnak elküldeni kívánt naplófájlokat.

    ![Helyettesítő szöveg](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  A visszajelzés elküldése végfelhasználói licencszerződés elfogadásához.

3.  Válassza ki **küldés** az eredményeket küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

- [Figyelheti és kezelheti a VaaS portálon tesztek](azure-stack-vaas-monitor-test.md)
