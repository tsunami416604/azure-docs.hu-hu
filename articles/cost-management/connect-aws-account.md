---
title: "Az Amazon Web Services-fiók csatlakozni Azure költség Management |} Microsoft Docs"
description: "Csatlakoztassa az Amazon Web Services-fiókot az költség jelentések költség- és használati adatainak megtekintéséhez."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 4a0280420132aad9f1e0b17d5998ec225bb0eaa1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Csatlakozás az Amazon Web Services-fiók

Az Amazon Web Services (AWS) fiók csatlakozni az Azure költség felügyeleti lehetőségei vannak. Egy IAM-szerepkörrel, vagy csak olvasható IAM felhasználói fiókkal is elérheti. A IAM-szerepkör használata ajánlott, mivel lehetővé teszi a megbízható entitások meghatározott engedélyekkel rendelkező hozzáférés delegálására. A IAM-szerepkör nem kell megosztani a hosszú távú elérési kulcsok. Költség felügyeleti AWS fiókkal csatlakozik, a költség jelentések költség- és használati adatok érhető el. Ez a dokumentum végigvezeti mindkét lehetőség.

Az AWS IAM-identitások kapcsolatos további információkért lásd: [(felhasználók, csoportok és szerepkörök) identitások](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

## <a name="aws-role-based-access"></a>AWS szerepkörön alapuló hozzáférés

A következő szakaszok végigvezetik Önt a felügyeleti költségek való hozzáférés biztosításához írásvédett IAM szerepkör létrehozása.

### <a name="get-your-cost-management-account-external-id"></a>Az költség felügyeleti fiók külső beszerzése

Az első lépés az egyedi csatlakozás jelszó lekérése az Azure költség felügyeleti portálra. Szerepel, mint a AWS a **külső azonosító**.

1. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon arra [https://azure.cloudyn.com](https://azure.cloudyn.com) , és jelentkezzen be.
2. Kattintson a **beállítások** (fogaskerék ikonjára ikon), és válassza **felhő fiókok**.
3. Fiókok kezelése, válassza ki a **AWS fiókok** fülre, majd **új hozzáadása +**.
4. Az a **AWS fiók hozzáadása** párbeszédpanelen, a Másolás a **külső azonosító** , és mentse ezt az értéket az AWS szerepkör létrehozása a következő szakaszban ismertetett visszaállítási lépésekkel. Az alábbi ábrán a példa azonosító: _Cloudyn_. Az azonosító különbözik.  
    ![Külső azonosítója](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS olvasási szerepkörön alapuló hozzáférés hozzáadása

1. Jelentkezzen be https://console.aws.amazon.com/iam/home, és válassza ki az AWS konzol **szerepkörök**.
2. Kattintson a **szerepkör létrehozása** majd **egy másik AWS fiók**.
3. Illessze be az identitás `432263259397` a a **Fiókazonosító** mező. A Fiókazonosító költség felügyeleti adatokat gyűjtő-fiókot kell használnia.
4. A **beállítások**, jelölje be **szükséges külső azonosító** illessze be az értéket, amelyet korábban az a **külső azonosító** mezőben, majd kattintson **tovább: Engedélyek**.  
    ![Szerepkör létrehozása](./media/connect-aws-account/create-role01.png)
5. A **engedélyek házirendek csatolása**, a a **házirendtípus** keresési a Szűrő mezőbe, írja be `ReadOnlyAccess`, jelölje be **ReadOnlyAccess**, kattintson a **tovább: Tekintse át**.  
    ![Csak olvasási hozzáféréssel](./media/connect-aws-account/readonlyaccess.png)
6. Az Áttekintés lap biztosítása a beállítások helyesek, és írja be a **szerepkörnév**. Például *Azure-költség-Mgt*. Adjon meg egy **szerepkör leírása**. Például _szerepkör-hozzárendelés az Azure költség Management_, majd kattintson a **szerepkör létrehozása**.
7. Az a **szerepkörök** listában, kattintson a létrehozott szerepkörre, és másolja a **szerepkör információ** értéket összesítő lapján. A szerepkör-információ érték későbbi használat Azure költség felügyeleti rögzítheti a konfigurációt.  
    ![Szerepkör információ](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Költség felügyeleti AWS IAM szerepkör hozzáférés konfigurálása

1. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon a https://azure.cloudyn.com/, és jelentkezzen be.
2. Kattintson a **beállítások** (fogaskerék ikonjára ikon), és válassza **felhő fiókok**.
3. Fiókok kezelése, válassza ki a **AWS fiókok** fülre, majd **új hozzáadása +**.
4. A **fióknév**, írja be a fiók nevét.
5. A **hozzáférési típus**, jelölje be **IAM szerepkör**.
6. Az a **szerepkör információ** mezőben illessze be a korábban kimásolt értéket, és kattintson a **mentése**.  
    ![AWS fiók állapota](./media/connect-aws-account/aws-account-status01.png)

Az AWS fiók fiókok listája megjelenik. A **Tulajdonosazonosítót** felsorolt megegyezik-e a szerepkör-információ. A **Fiókállapotot** rendelkeznie kell egy zöld pipa szimbólumot.

Költség felügyeleti elindul az adatgyűjtés, valamint a jelentések való feltöltéséhez. Egyes optimalizálási jelentések azonban szükség lehet adatokat néhány nap elteltével pontos javaslatok jönnek létre.

## <a name="aws-user-based-access"></a>Felhasználó-alapú hozzáférés AWS

A következő szakaszok végigvezetik Önt a felügyeleti költségek való hozzáférés biztosításához írásvédett felhasználó létrehozása.

### <a name="add-aws-read-only-user-based-access"></a>AWS olvasási felhasználó-alapú hozzáférés hozzáadása

1. Jelentkezzen be https://console.aws.amazon.com/iam/home, és válassza ki az AWS konzol **felhasználók**.
2. Kattintson a **felhasználó hozzáadása**.
3. Az a **felhasználónév** mezőbe írjon be egy felhasználónevet.
4. A **hozzáférési típus**, jelölje be **programozott hozzáférés** kattintson **tovább: engedélyek**.  
    ![felhasználó hozzáadása](./media/connect-aws-account/add-user01.png)
5. Engedélyek, válassza a **közvetlenül csatolni a meglévő házirendek**.
6. A **engedélyek házirendek csatolása**, a a **házirendtípus** keresési a Szűrő mezőbe, írja be `ReadOnlyAccess`, jelölje be **ReadOnlyAccess**, és kattintson a **következő : Tekintse át**.  
    ![A felhasználói engedélyek beállítása](./media/connect-aws-account/set-permission-for-user.png)
7. Az Áttekintés lap biztosítása a beállítások helyesek, majd kattintson az **a felhasználó létrehozása**.
8. A teljes oldalon a hozzáférési kulcs azonosítója és a titkos hívóbetű jelennek meg. Ezen információk használatával költség felügyeleti regisztrálásának konfigurálása.
9. Kattintson a **töltse le a .csv** , és mentse a credentials.csv fájlt egy biztonságos helyre.  
    ![Töltse le a hitelesítő adatok](./media/connect-aws-account/download-csv.png)


### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Költség felügyeleti AWS IAM felhasználó-alapú hozzáférés konfigurálása

1. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon a https://azure.cloudyn.com/, és jelentkezzen be.
2. Kattintson a **beállítások** (fogaskerék ikonjára ikon), és válassza **felhő fiókok**.
3. Fiókok kezelése, válassza ki a **AWS fiókok** fülre, majd **új hozzáadása +**.
4. A **fióknév**, írja be a fiók nevét.
5. A **hozzáférési típus**, jelölje be **IAM felhasználói**.
6. A **hozzáférési kulcs**, illessze be a **hozzáférési kulcs azonosítója** érték a credentials.csv fájlból.
7. A **titkos kulcs**, illessze be a **titkos hívóbetű** credentials.csv fájlból értékét, és kattintson a **mentése**.  
    ![AWS felhasználói fiók állapota](./media/connect-aws-account/aws-user-account-status.png)

Az AWS fiók fiókok listája megjelenik. A **Fiókállapotot** rendelkeznie kell egy zöld pipa szimbólumot.

Költség felügyeleti elindul az adatgyűjtés, valamint a jelentések való feltöltéséhez. Egyes optimalizálási jelentések azonban szükség lehet adatokat néhány nap elteltével pontos javaslatok jönnek létre.

## <a name="next-steps"></a>További lépések

- További információ az Azure költség Management, továbbra is a [tekintse át a használati és költségek](tutorial-review-usage.md) oktatóanyag költség-kezelésre.
