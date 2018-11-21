---
title: Csatlakozás Amazon Web Services-fiók az Azure-ban a Cloudyn |} A Microsoft Docs
description: Csatlakozás egy Amazon Web Services-fiók költség- és használati adatok megtekintéséhez a Cloudyn jelentésekben.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 45baefbea3d2bd03098c045c42dc67dccceba8c2
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275367"
---
# <a name="connect-an-amazon-web-services-account"></a>Csatlakozás Amazon Web Services-fiók

Az Amazon Web Services (AWS) fiók csatlakoztatásához a Cloudyn két lehetősége van. Az IAM-szerepkörrel, vagy egy csak olvasható IAM felhasználói fiókkal is lehet kapcsolódni. Az IAM-szerepkör ajánlott, mert lehetővé teszi, hogy meghatározott engedélyekkel a megbízható entitások hozzáférés delegálására. Az IAM-szerepkör nem igényel hosszú távú tárelérési kulcsok megoszthatja. AWS-fiók a Cloudynben a kapcsolódás után a költség- és használati adatokat a Cloudyn jelentésekben érhető el. Ez a dokumentum végigvezeti mindkét lehetőség.

Az AWS IAM-identitások kapcsolatos további információkért lásd: [(felhasználók, csoportok és szerepkörök) identitások](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Ezenkívül engedélyeznie az AWS részletes jelentések számlázási és a egy AWS simple storage service (S3) gyűjtőbe az információkat tárolja. Részletes számlázási jelentések címke- és erőforrás adatok óránként díjak tartalmazzák. A jelentések tárolására lehetővé teszi, hogy a Cloudyn kérheti le azokat, a gyűjtő és az információk megjelenítése a jelentésekben.


## <a name="aws-role-based-access"></a>Az AWS-szerepkörön alapuló hozzáférés

A következő szakaszok végigvezetik egy csak olvasható IAM szerepkör számára hozzáférést biztosítsunk a Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>A Cloudyn-fiók külső azonosító beszerzése

Az első lépés, hogy az egyedi kapcsolat hozzáférési kód lekérése a Cloudyn portálon. Szerepel, mint az AWS a **külső azonosító**.

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy navigáljon arra [ https://azure.cloudyn.com ](https://azure.cloudyn.com) , és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd **Felhőfiókok**.
3. A fiókok kezelése, válassza ki a **az AWS-fiókok** fülre, majd **új hozzáadása +**.
4. Az a **AWS-fiók hozzáadása** párbeszédpanelen másolása a **külső azonosító** és mentse az értéket az AWS-szerepkör létrehozásának lépéseit a következő szakaszban. A külső azonosító egyediségét a fiókjához. Az alábbi ábrán a külső azonosító példája _Contoso_ egy szám követ. Az azonosító különbözik.  
    ![Külső azonosító](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Az AWS csak olvasható szerepkör alapú hozzáférés hozzáadása

1. Jelentkezzen be az AWS kezelőpultját https://console.aws.amazon.com/iam/home válassza **szerepkörök**.
2. Kattintson a **szerepkör létrehozása** majd **egy másik AWS-fiók**.
3. Az a **Fiókazonosító** mezőbe illessze be `432263259397`. A Fiókazonosító az AWS által a Cloudyn-szolgáltatáshoz rendelt Cloudyn gyűjtő fiók. Használhatja a pontos Fiókazonosító látható.
4. A **beállítások**válassza **külső ID használatának megkövetelése**. Illessze be az egyedi érték, amely korábban átmásolta a **külső azonosító** a Cloudyn mezőt. Kattintson a **tovább: engedélyek**.  
    ![Szerepkör létrehozása](./media/connect-aws-account/create-role01.png)
5. Alatt **engedélyházirend csatolása**, a a **házirendtípus** írja be a Szűrő mezőbe keresési `ReadOnlyAccess`, jelölje be **ReadOnlyAccess**, majd kattintson a **tovább: Felülvizsgálat**.  
    ![Csak olvasási hozzáféréssel](./media/connect-aws-account/readonlyaccess.png)
6. Az Áttekintés lap, győződjön meg, hogy a beállítások helyességét, majd írja be a **szerepkörnév**. Ha például *Azure-költségek-Mgt*. Adjon meg egy **szerepkör leírása**. Ha például _Cloudyn szerepkör-hozzárendelés_, majd kattintson a **szerepkör létrehozása**.
7. Az a **szerepkörök** listában, kattintson a létrehozott szerepkört, és másolja a **szerepkör információ** értéket az Összegzés lapon. A szerepkör információ (Amazon erőforrás neve) értéke is regisztrálhatja a konfigurációt, a Cloudyn később használni.  
    ![Szerepkör információ](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>A Cloudyn AWS IAM szerepköralapú hozzáférés konfigurálása

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy navigáljon arra https://azure.cloudyn.com/ , és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd **Felhőfiókok**.
3. A fiókok kezelése, válassza ki a **az AWS-fiókok** fülre, majd **új hozzáadása +**.
4. A **fióknév**, írja be a fiók nevét.
5. A **hozzáférés típusa**válassza **IAM szerepkör**.
6. Az a **szerepkör információ** mezőben illessze be a korábban kimásolt értéket, és kattintson a **mentése**.  
    ![Az AWS-fiók mező hozzáadása](./media/connect-aws-account/add-aws-account-box.png)


AWS-fiók fiókok listája jelenik meg. A **tulajdonos azonosítója** felsorolt megegyezik a szerepkör információ értékkel. A **fiók állapota** rendelkeznie kell egy zöld pipa szimbólumot, jelezve, hogy a Cloudyn hozzáférhet az AWS-fiók. Amíg nem engedélyezi a részletes AWS számlázás, az összevonási állapotú **önálló**.

![Az AWS-fiók állapota](./media/connect-aws-account/aws-account-status01.png)

A Cloudyn elindítja az adatok gyűjtése és jelentések feltöltése. Ezután [részletes AWS számlázás](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS – felhasználó-alapú hozzáférés

A következő szakaszok végigvezetik egy csak olvasható hozzáférést biztosítsunk a Cloudyn-felhasználó létrehozása.

### <a name="add-aws-read-only-user-based-access"></a>Az AWS írásvédett felhasználó-alapú hozzáférés hozzáadása

1. Jelentkezzen be az AWS kezelőpultját https://console.aws.amazon.com/iam/home válassza **felhasználók**.
2. Kattintson a **felhasználó hozzáadása**.
3. Az a **felhasználónév** mezőbe írja be a felhasználónevet.
4. A **típus eléréséhez**, jelölje be **programozás alapú hozzáférést** kattintson **tovább: engedélyek**.  
    ![Felhasználó hozzáadása](./media/connect-aws-account/add-user01.png)
5. Engedélyek kiválasztása **a meglévő szabályzatok közvetlen csatolása**.
6. Alatt **engedélyházirend csatolása**, a a **házirendtípus** írja be a Szűrő mezőbe keresési `ReadOnlyAccess`válassza **ReadOnlyAccess**, és kattintson a **tovább : Tekintse át**.  
    ![A felhasználói engedélyek beállítása](./media/connect-aws-account/set-permission-for-user.png)
7. Az Áttekintés lap, ellenőrizze, helyes-e a választott beállításokat, majd kattintson a **felhasználó létrehozása**.
8. A teljes lapon jelenik meg a hozzáférési kulcs azonosítója és kulcsa hozzáférési kulccsal. Ezen információk használatával a Cloudyn regisztrációs konfigurálása.
9. Kattintson a **letöltése .csv** és mentse a credentials.csv fájlt egy biztonságos helyre.  
    ![Hitelesítő adatainak letöltése](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>A Cloudyn AWS IAM felhasználóalapú hozzáférés konfigurálása

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com/ webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd **Felhőfiókok**.
3. A fiókok kezelése, válassza ki a **az AWS-fiókok** fülre, majd **új hozzáadása +**.
4. A **fióknév**, írja be egy fiók nevét.
5. A **hozzáférés típusa**válassza **IAM felhasználói**.
6. A **hívóbetű**, illessze be a **hozzáférési kulcs azonosítója** érték a credentials.csv fájlból.
7. A **titkos kulcs**, illessze be a **titkos hívóbetűje** értéket a credentials.csv fájlból, és kattintson a **mentése**.  

AWS-fiók fiókok listája jelenik meg. A **fiók állapota** rendelkeznie kell egy zöld pipa szimbólumot.

A Cloudyn elindítja az adatok gyűjtése és jelentések feltöltése. Ezután [részletes AWS számlázás](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Részletes AWS Számlázás engedélyezése

Az alábbi lépések segítségével az AWS-szerepkör információ beolvasása. A szerepkör információ segítségével egy számlázási gyűjtőhöz olvasási engedélyeket ad.

1. Jelentkezzen be az AWS kezelőpultját https://console.aws.amazon.com válassza **szolgáltatások**.
2. A szolgáltatás a Keresés mezőbe írja be *IAM*, és válassza ezt a lehetőséget.
3. Válassza ki **szerepkörök** elemet a bal oldali menüben.
4. A szerepkörök listájában válassza ki a Cloudyn-hozzáférést létrehozott szerepkör.
5. Másolja a Szerepkörök összegzése lapon kattintson a **szerepkör információ**. Tartsa meg a szerepkör információ hasznos a későbbi lépésekben.

### <a name="create-an-s3-bucket"></a>Hozzon létre egy S3 gyűjtő

Létrehozhat egy S3 gyűjtőt részletes számlázási információk tárolására.

1. Jelentkezzen be az AWS kezelőpultját https://console.aws.amazon.com válassza **szolgáltatások**.
2. A szolgáltatás a Keresés mezőbe írja be *S3*, és válassza ki **S3**.
3. Az Amazon S3 lapon kattintson a **létrehozás gyűjtőbe**.
4. A gyűjtő létrehozása varázslóban válassza ki a Bucket nevét és régióban, és kattintson **tovább**.  
    ![Gyűjtő létrehozása](./media/connect-aws-account/create-bucket.png)
5. Az a **tulajdonságainak beállítása** lapon használja az alapértelmezett értékeket, és kattintson a **tovább**.
6. Kattintson az Áttekintés lap **létrehozás gyűjtőbe**. A gyűjtő lista jelenik meg.
7. Kattintson a gyűjtőhöz, Ön által létrehozott, és válassza ki a **engedélyek** lapot, majd **gyűjtőbe házirend**. A gyűjtő Helyicsoportházirend-szerkesztő megnyitása.
8. Másolja az alábbi példa JSON, és illessze be a gyűjtő Helyicsoportházirend-szerkesztő.
  - Cserélje le `<BillingBucketName>` az S3 gyűjtő nevével.
  - Cserélje le `<ReadOnlyUserOrRole>` -szerepkör, illetve felhasználói információ, korábban kimásolt rendelkezett.

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


### <a name="enable-aws-billing-reports"></a>Az AWS számlázási jelentések engedélyezése

Miután létrehozta és az S3 gyűjtőt konfigurálja, lépjen [számlázási beállítások](https://console.aws.amazon.com/billing/home?#/preference) az AWS konzolon.

1. A beállítások lapon válassza ki a **számlázási jelentések fogadásához**.
2. A **számlázási jelentések fogadásához**, adja meg a gyűjtőhöz, Ön által létrehozott nevét, majd kattintson **ellenőrizze**.  
3. Válassza ki, mind a négy jelentési lehetőségek részletességgel, és kattintson a **beállítások mentése**.  
    ![Jelentések engedélyezése](./media/connect-aws-account/enable-reports.png)

A Cloudyn részletes számlázási adatait kérdezi le az S3 gyűjtőt, és feltölti a jelentések, részletes számlázási engedélyezése után. Mindaddig, amíg a Cloudyn-konzolon megjelenik a részletes számlázási adatok 24 órát is igénybe vehet. A részletes számlázási adatok nem érhető el, a fiók összevonása állapotú **konszolidált**. Fiók állapota jelenik meg **befejezve**.

![Fiók összesített állapota](./media/connect-aws-account/consolidated-status.png)

Néhány nap adatait egy megfelelő minta adatméret pontos javaslatokat beolvasni az optimalizálási jelentések némelyike lehet szükség.

## <a name="next-steps"></a>További lépések

- A Cloudyn kapcsolatos további információkért folytassa az [tekintse át a használat és költségek](tutorial-review-usage.md) Cloudyn oktatóanyagot.
