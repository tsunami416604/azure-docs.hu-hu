---
title: Az Amazon Web Services-fiók csatlakozni Azure költség Management |} Microsoft Docs
description: Csatlakoztassa az Amazon Web Services-fiókot az költség jelentések költség- és használati adatainak megtekintéséhez.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 0c18fc065ae4d9a9401a8d603f051e9d6236c538
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="connect-an-amazon-web-services-account"></a>Csatlakozás az Amazon Web Services-fiók

Az Amazon Web Services (AWS) fiók csatlakozni az Azure költség felügyeleti lehetőségei vannak. Egy IAM-szerepkörrel, vagy csak olvasható IAM felhasználói fiókkal is elérheti. A IAM-szerepkör használata ajánlott, mivel lehetővé teszi a megbízható entitások meghatározott engedélyekkel rendelkező hozzáférés delegálására. A IAM-szerepkör nem kell megosztani a hosszú távú elérési kulcsok. Költség felügyeleti AWS fiókkal csatlakozik, a költség jelentések költség- és használati adatok érhető el. Ez a dokumentum végigvezeti mindkét lehetőség.

Az AWS IAM-identitások kapcsolatos további információkért lásd: [(felhasználók, csoportok és szerepkörök) identitások](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Emellett engedélyezi a részletes AWS jelentések számlázási és az információkat tárolja az AWS egyszerű tárolási szolgáltatás (S3) gyűjtőjét. Részletes számlázási jelentések adatforgalmi díjak óránként címke- és erőforrás információkat tartalmaznak. A jelentések tárolása lehetővé teszi, hogy a költség felügyeleti le azokat a gyűjtő és az információk megjelenítéséhez a jelentésben.


## <a name="aws-role-based-access"></a>AWS szerepkörön alapuló hozzáférés

A következő szakaszok végigvezetik Önt a felügyeleti költségek való hozzáférés biztosításához írásvédett IAM szerepkör létrehozása.

### <a name="get-your-cost-management-account-external-id"></a>Az költség felügyeleti fiók külső beszerzése

Az első lépés az egyedi csatlakozás jelszó lekérése az Azure költség felügyeleti portálra. Szerepel, mint a AWS a **külső azonosító**.

1. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon arra [ https://azure.cloudyn.com ](https://azure.cloudyn.com) , és jelentkezzen be.
2. Válassza a fogaskerékre szimbólum **felhő fiókok**.
3. Fiókok kezelése, válassza ki a **AWS fiókok** fülre, majd **új hozzáadása +**.
4. Az a **AWS fiók hozzáadása** párbeszédpanelen, a Másolás a **külső azonosító** , és mentse a érték AWS szerepkör létrehozása a következő szakaszban ismertetett visszaállítási lépésekkel. A külső azonosító nem egyedi a fiókjához. Az alábbi ábrán a külső azonosító példája _Contoso_ egy szám követ. Az azonosító különbözik.  
    ![Külső azonosítója](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS olvasási szerepkörön alapuló hozzáférés hozzáadása

1. Jelentkezzen be az AWS konzol https://console.aws.amazon.com/iam/home válassza **szerepkörök**.
2. Kattintson a **szerepkör létrehozása** majd **egy másik AWS fiók**.
3. Az a **Fiókazonosító** mezőbe illessze be `432263259397`. A Fiókazonosító Cloudyn szolgáltatáshoz AWS által hozzárendelt költség felügyeleti adatokat gyűjtő fiókjának. Használja a pontos Fiókazonosító látható.
4. A **beállítások**, jelölje be **szükséges külső azonosító**. Az egyedi érték, a korábban kimásolt illessze be a **külső azonosító** költség felügyeleti mezőbe. Kattintson a **tovább: engedélyek**.  
    ![Szerepkör létrehozása](./media/connect-aws-account/create-role01.png)
5. A **engedélyek házirendek csatolása**, a a **házirendtípus** keresési a Szűrő mezőbe, írja be `ReadOnlyAccess`, jelölje be **ReadOnlyAccess**, kattintson a **tovább: Tekintse át**.  
    ![Csak olvasási hozzáféréssel](./media/connect-aws-account/readonlyaccess.png)
6. Az Áttekintés lap biztosítása a beállítások helyesek, és írja be a **szerepkörnév**. Például *Azure-költség-Mgt*. Adjon meg egy **szerepkör leírása**. Például _szerepkör-hozzárendelés az Azure költség Management_, majd kattintson a **szerepkör létrehozása**.
7. Az a **szerepkörök** listában, kattintson a létrehozott szerepkörre, és másolja a **szerepkör információ** értéket összesítő lapján. A szerepkör-információ (Amazon erőforrásnév) értéket későbbi használat Azure költség felügyeleti rögzítheti a konfigurációt.  
    ![Szerepkör információ](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Költség felügyeleti AWS IAM szerepkör hozzáférés konfigurálása

1. Nyissa meg a Cloudyn portált Azure-portálról, vagy navigáljon arra https://azure.cloudyn.com/ , és jelentkezzen be.
2. Válassza a fogaskerékre szimbólum **felhő fiókok**.
3. Fiókok kezelése, válassza ki a **AWS fiókok** fülre, majd **új hozzáadása +**.
4. A **fióknév**, írja be a fiók nevét.
5. A **hozzáférési típus**, jelölje be **IAM szerepkör**.
6. Az a **szerepkör információ** mezőben illessze be a korábban kimásolt értéket, és kattintson a **mentése**.  
    ![A Hozzáadás AWS fiók használata](./media/connect-aws-account/add-aws-account-box.png)


Az AWS fiók fiókok listája megjelenik. A **Tulajdonosazonosítót** felsorolt megegyezik-e a szerepkör-információ. A **Fiókállapotot** rendelkeznie kell egy zöld pipa szimbólumot jelzi, hogy költség felügyeleti hozzáférhet-e a AWS fiókját. Amíg nem engedélyezi a részletes AWS számlázási, megjelenik-e a konszolidáció állapot szerint **önálló**.

![AWS fiók állapota](./media/connect-aws-account/aws-account-status01.png)

Költség felügyeleti elindul az adatgyűjtés, valamint a jelentések való feltöltéséhez. Ezt követően [részletes AWS számlázási engedélyezése](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Felhasználó-alapú hozzáférés AWS

A következő szakaszok végigvezetik Önt a felügyeleti költségek való hozzáférés biztosításához írásvédett felhasználó létrehozása.

### <a name="add-aws-read-only-user-based-access"></a>AWS olvasási felhasználó-alapú hozzáférés hozzáadása

1. Jelentkezzen be az AWS konzol https://console.aws.amazon.com/iam/home válassza **felhasználók**.
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

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com/ webhelyre, és jelentkezzen be.
2. Válassza a fogaskerékre szimbólum **felhő fiókok**.
3. Fiókok kezelése, válassza ki a **AWS fiókok** fülre, majd **új hozzáadása +**.
4. A **fióknév**, írja be a fiók nevét.
5. A **hozzáférési típus**, jelölje be **IAM felhasználói**.
6. A **hozzáférési kulcs**, illessze be a **hozzáférési kulcs azonosítója** érték a credentials.csv fájlból.
7. A **titkos kulcs**, illessze be a **titkos hívóbetű** credentials.csv fájlból értékét, és kattintson a **mentése**.  

Az AWS fiók fiókok listája megjelenik. A **Fiókállapotot** rendelkeznie kell egy zöld pipa szimbólumot.

Költség felügyeleti elindul az adatgyűjtés, valamint a jelentések való feltöltéséhez. Ezt követően [részletes AWS számlázási engedélyezése](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Részletes AWS számlázási engedélyezése

Az alábbi lépések segítségével az AWS szerepkör információ lekérése. A szerepkör-információ segítségével egy számlázási gyűjtőben olvasási engedélyt.

1. Jelentkezzen be az AWS konzol https://console.aws.amazon.com válassza **szolgáltatások**.
2. A szolgáltatás Keresés mezőbe írja be *IAM*, és válassza ki ezt a beállítást.
3. Válassza ki **szerepkörök** a bal oldali menüből.
4. A szerepkörök, jelölje ki a szerepkör Cloudyn hozzáférés létrehozott.
5. Másolja a Szerepkörök összegzése lapon kattintson a **szerepkör információ**. Tartsa meg a szerepkör-információ későbbi lépéseire lesz szüksége.

### <a name="create-an-s3-bucket"></a>Hozzon létre egy S3 gyűjtő

Létrehozhat egy S3 gyűjtő részletes számlázási adatok tárolására.

1. Jelentkezzen be az AWS konzol https://console.aws.amazon.com válassza **szolgáltatások**.
2. A szolgáltatás Keresés mezőbe írja be *S3*, és válassza ki **S3**.
3. Az Amazon S3 lapján kattintson a **létrehozás gyűjtő**.
4. A gyűjtő létrehozása varázslóban válasszon ki egy gyűjtő nevét és a régiót, és kattintson a **következő**.  
    ![Gyűjtő létrehozása](./media/connect-aws-account/create-bucket.png)
5. Az a **tulajdonságainak** lapon használja az alapértelmezett értékeket, és kattintson a **következő**.
6. Az Áttekintés lap, kattintson a **létrehozás gyűjtő**. A gyűjtő listája jelenik meg.
7. Kattintson a létrehozott gyűjtő, és válassza a **engedélyek** fülre, majd jelölje **gyűjtőjét házirend**. A gyűjtő Helyicsoportházirend-szerkesztő megnyitása.
8. Másolja az alábbi JSON-példa, és illessze be a gyűjtő csoportházirend-szerkesztőt.
  - Cserélje le `<BillingBucketName>` a S3 gyűjtő nevével.
  - Cserélje le `<ReadOnlyUserOrRole>` szerepkör, illetve felhasználói információ korábban kimásolt volna.

  ```
  {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
  }
  ```

9. Kattintson a **Save** (Mentés) gombra.  
    ![Gyűjtő Helyicsoportházirend-szerkesztő](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Jelentések számlázási AWS engedélyezése

Létrehozott, és konfigurálja a S3 gyűjtő, navigáljon a [számlázási beállítások](https://console.aws.amazon.com/billing/home?#/preference) az AWS konzol.

1. A beállítások lapon válassza az **számlázási jelentések kap**.
2. A **számlázási jelentések kap**, adja meg a létrehozott gyűjtő nevét, és kattintson a **ellenőrizze**.  
3. Válassza ki, mind a négy lehetőségeket granularitási jelentést, és kattintson a **beállítások mentése**.  
    ![Lehetővé teszi jelentések](./media/connect-aws-account/enable-reports.png)

Költség felügyeleti részletes számlázási adatait kérdezi le a S3 gyűjtő tölti fel jelentések részletes számlázási engedélyezése után. A Cloudyn konzolon részletes elszámolási adatok megjelenéséig akár 24 óráig is eltarthat. Részletes elszámolási adatok nem érhető el, a fiók-összevonási állapota akkor jelenik meg, mint **konszolidált**. Fiók állapota akkor jelenik meg, mint a **befejezve**.

![Fiók konszolidált állapota](./media/connect-aws-account/consolidated-status.png)

Az optimalizálás jelentések némelyike lehet szükség az adatok lekérésére egy megfelelő adatok mintájának méretét pontos javaslatok néhány nap múlva.

## <a name="next-steps"></a>További lépések

- További információ az Azure költség Management, továbbra is a [tekintse át a használati és költségek](tutorial-review-usage.md) oktatóanyag költség-kezelésre.
