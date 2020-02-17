---
title: Amazon Web Services-fiók csatlakoztatása a Cloudynhez az Azure-ban | Microsoft Docs
description: Csatlakoztasson egy Amazon Web Services-fiókot a költségek és a használati adatok Cloudyn-jelentésekben való megtekintéséhez.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: 28229ad71327daefb8e42881cf001b6a3ddd3a53
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086845"
---
# <a name="connect-an-amazon-web-services-account"></a>Amazon Web Services-fiók csatlakoztatása

Az Amazon Web Services- (AWS-) fiók kétféleképpen csatlakoztatható a Cloudynhez. Csatlakozhat IAM-szerepkörrel vagy egy csak olvasható IAM felhasználói fiókkal. Az IAM-szerepkör használata ajánlott, mert lehetővé teszi, hogy meghatározott engedélyekkel delegáljon hozzáférést a megbízható entitásokhoz. Az IAM-szerepkör használatakor nem kell hosszú távú hozzáférési kulcsokat megosztania. Miután csatlakoztatta az AWS-fiókot a Cloudynhez, a költség- és használati adatok elérhetők lesznek a Cloudyn-jelentésekben. Ez a dokumentum mindkét módszert bemutatja.

További információ az AWS IAM-identitásokról: [Identitások (felhasználók, csoportok, szerepkörök)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Emellett engedélyezheti az AWS részletes számlázási jelentéseit és egy AWS Simple Storage Service- (S3-) tárolóban tárolhatja az adatokat. A részletes számlázási jelentések címke- és erőforrásadatokkal együtt tartalmazzák a költségeket, óránkénti lebontás alapján. A jelentések tárolása lehetővé teszi, hogy a Cloudyn lekérje őket a tárolóból, és megjelenítse az adatokat a jelentésekben.


## <a name="aws-role-based-access"></a>Szerepköralapú AWS-hozzáférés

A következő szakaszok végigvezetik egy csak olvasható IAM-szerepkör létrehozásának folyamatán, amely hozzáférést biztosít a Cloudynhez.

### <a name="get-your-cloudyn-account-external-id"></a>A Cloudyn-fiók külső azonosítójának lekérése

Első lépésként keresse meg az egyedi kapcsolati jelszót a Cloudyn portálján. Az AWS-ben ez szolgál **külső azonosítóként**.

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a [https://azure.cloudyn.com](https://azure.cloudyn.com) webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd válassza a **Cloud Accounts** (Cloud-fiókok) lehetőséget.
3. Az Accounts Management (Fiókkezelés) területen válassza az **AWS Accounts** (AWS-fiókok) lapot, majd kattintson az **Add new +** (Új hozzáadása +) elemre.
4. Az **Add AWS Account** (AWS-fiók hozzáadása) párbeszédpanelen, másolja és mentse az **External ID** (Külső azonosító) értékét, amelyet a következő szakaszban használ majd az AWS-szerepkörök létrehozásakor. A fiók egyedi külső azonosítóval rendelkezik. A következő képen a külső azonosító a _Contoso_ szóból és az azt követő számsorból áll. Saját azonosítója ettől eltér.  
    ![Az Add AWS Account (AWS-fiók hozzáadása) panelen látható külső azonosító](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Csak olvasható, szerepköralapú AWS-hozzáférés hozzáadása

1. Jelentkezzen be az AWS-konzolba a [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) webhelyen, és válassza a **Roles** (Szerepkörök) lehetőséget.
2. Kattintson a **Create Role** (Szerepkör létrehozása) elemre, majd válassza az **Another AWS account** (Másik AWS-fiók) lehetőséget.
3. Az **Account ID** (Fiókazonosító) mezőbe másolja be a `432263259397` értéket. Ez a fiókazonosító az AWS által a Cloudyn szolgáltatáshoz hozzárendelt Cloudyn adatgyűjtő fiók. Használja a megjelenített fiókazonosítót.
4. Az **Options** (Beállítások) mellett válassza a **Require external ID** (Külső azonosító szükséges) elemet. Illessze be a korábban a Cloudyn **External ID** (Külső azonosító) mezőjéből kimásolt egyedi értéket. Ezután kattintson a **Tovább: Permissions** (Következő: Engedélyek) elemre.  
    ![külső azonosító beillesztése a Cloudynből a Create role (Szerepkör létrehozása) lapon](./media/connect-aws-account/create-role01.png)
5. Az **Attach permissions policies** (Engedélyszabályzatok csatolása) területen, a **Policy type** (Szabályzattípus) keresőmezőbe írja be a `ReadOnlyAccess` kifejezést, válassza a **ReadOnlyAccess** lehetőséget, majd kattintson a **Next: Review** (Következő: Áttekintés) elemre.  
    ![csak olvasási hozzáférés kiválasztása a szabályzatnevek listájából](./media/connect-aws-account/readonlyaccess.png)
6. A Review (Áttekintés) lapon ellenőrizze, hogy a beállítások helyesek-e, majd adja meg a szerepkör nevét (**Role name**). Például: *Azure-Cost-Mgt*. Adja meg a szerepkör leírását (**Role description**). Ez lehet például a _Szerepkör-hozzárendelés a Cloudynhez_. Ezt követően kattintson a **Create role** (Szerepkör létrehozása) elemre.
7. A **Roles** (Szerepkörök) listájában kattintson a létrehozott szerepkörre, majd a Summary (Összefoglalás) lapról másolja ki a **Role ARN** (Szerepkör ARN-je) értéket. A Role ARN (Amazon-erőforrásneve) értéket később használhatja, amikor a konfigurációt regisztrálja a Cloudynbe.  
    ![a szerepkör ARN-jének másolása a Summary (Összefoglalás) lapról](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Szerepköralapú AWS IAM-hozzáférés beállítása a Cloudynben

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com/ webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd válassza a **Cloud Accounts** (Cloud-fiókok) lehetőséget.
3. Az Accounts Management (Fiókkezelés) területen válassza az **AWS Accounts** (AWS-fiókok) lapot, majd kattintson az **Add new +** (Új hozzáadása +) elemre.
4. Az **Account Name** (Fiók neve) mezőbe írja be a fiók nevét.
5. Az **Access Type** (Hozzáférés típusa) mellett, válassza az **IAM Role** (IAM-szerepkör) lehetőséget.
6. A **Role ARN** (Szerepkör ARN-je) mezőbe illessze be a korábban kimásolt értéket, majd kattintson a **Save** (Mentés) elemre.  
    ![a szerepkör ARN-jének beillesztése az Add AWS Account (AWS-fiók hozzáadása) panelen](./media/connect-aws-account/add-aws-account-box.png)


Az AWS-fiók megjelenik a fiókok listájában. A listán szereplő **Owner ID** (Tulajdonosazonosító) megegyezik a Role ARN (Szerepkör ARN-je) értékkel. A **Account Status** (Fiók állapota) alatt egy zöld pipának kell megjelennie, amely azt jelzi, hogy a Cloudyn hozzáférhet az AWS-fiókhoz. Amíg nem engedélyezi az AWS részletes számlázását, az összevonás állapota **Standalone** (Különálló) lesz.

![Az AWS-fiók állapota az Accounts Management (Fiókkezelés) oldalon](./media/connect-aws-account/aws-account-status01.png)

A Cloudyn elkezdi gyűjteni az adatokat és kitölteni a jelentéseket. Ezután [engedélyezze az AWS részletes számlázását](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>Felhasználóalapú AWS-hozzáférés

A következő szakaszok végigvezetik egy csak olvasható felhasználó létrehozásának folyamatán, amely hozzáférést biztosít a Cloudynhez.

### <a name="add-aws-read-only-user-based-access"></a>Csak olvasható, felhasználóalapú AWS-hozzáférés hozzáadása

1. Jelentkezzen be az AWS-konzolba a [https://console.aws.amazon.com/iam/home](https://console.aws.amazon.com/iam/home) webhelyen, és válassza a **Users** (Felhasználók) lehetőséget.
2. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.
3. A **User name** (Felhasználónév) mezőbe írjon be egy felhasználónevet.
4. **Access type** (Hozzáférési típusként) esetében válassza a **Programmatic access** (Szoftveres hozzáférés) lehetőséget, majd kattintson a **Next: Permissions** (Következő: Engedélyek) elemre.  
    ![írjon be egy felhasználónevet az Add user (Felhasználó hozzáadása) oldalon](./media/connect-aws-account/add-user01.png)
5. Az engedélyekhez válassza az **Attach existing policies directly** (Meglévő szabályzatok közvetlen csatolása) lehetőséget.
6. Az **Attach permissions policies** (Engedélyszabályzatok csatolása) területen, a **Policy type** (Szabályzattípus) keresőmezőbe írja be a `ReadOnlyAccess` kifejezést, válassza a **ReadOnlyAccess** lehetőséget, majd kattintson a **Next: Review** (Következő: Áttekintés) elemre.  
    ![a ReadOnlyAccess lehetőség kiválasztása az engedélyek a felhasználóhoz való beállításához](./media/connect-aws-account/set-permission-for-user.png)
7. A Review (Áttekintés) lapon ellenőrizze, hogy a beállítások helyesek-e, majd kattintson a **Create user** (Felhasználó létrehozása) elemre.
8. A Complete (Befejezés) lapon megjelenik a hozzáférési kulcs azonosítója és a titkos hozzáférési kulcs. Ezekre az adatokra a Cloudynben való regisztráció konfigurálásához lesz szükség.
9. Kattintson a **Download .csv** (.csv letöltése) elemre, majd mentse a credentials.csv fájlt egy biztonságos helyre.  
    ![kattintson a Download .csv (.csv letöltése) elemre a hitelesítő adatok mentéséhez](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>Felhasználóalapú AWS IAM-hozzáférés beállítása a Cloudynben

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com/ webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd válassza a **Cloud Accounts** (Cloud-fiókok) lehetőséget.
3. Az Accounts Management (Fiókkezelés) területen válassza az **AWS Accounts** (AWS-fiókok) lapot, majd kattintson az **Add new +** (Új hozzáadása +) elemre.
4. Az **Account Name** (Fiók neve) mezőbe írja be a fiók nevét.
5. Az **Access Type** (Hozzáférés típusa) mellett válassza az **IAM User** (IAM-felhasználó) lehetőséget.
6. Illessze be az **Access Key** (Hozzáférési kulcs) mezőbe az **Access key ID** (Hozzáférési kulcs azonosító) értékét a credentials.csv fájlból.
7. Illessze be a **Secret Key** (Titkos kulcs) mezőbe a **Secret access key** (Titkos hozzáférési kulcs) értékét a credentials.csv fájlból, majd kattintson a **Save** (Mentés) elemre.  

Az AWS-fiók megjelenik a fiókok listájában. A **Account Status** (Fiók állapota) alatt egy zöld pipának kell megjelennie.

A Cloudyn elkezdi gyűjteni az adatokat és kitölteni a jelentéseket. Ezután [engedélyezze az AWS részletes számlázását](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Az AWS részletes számlázásának engedélyezése

Az AWS-szerepkör ARN-jének megismeréséhez hajtsa végre a következő lépéseket. A szerepkör ARN-jével olvasási engedélyt adhat egy számlázási tárolóhoz.

1. Jelentkezzen be az AWS-konzolba a [https://console.aws.amazon.com](https://console.aws.amazon.com) webhelyen, és válassza a **Services** (Szolgáltatások) lehetőséget.
2. A szolgáltatások keresésére szolgáló mezőbe írja be az *IAM* kifejezést, majd válassza ki ezt a beállítást.
3. A bal oldali menüben válassza a **Roles** (Szerepkörök) lehetőséget.
4. A szerepkörök listájában válassza ki a Cloudyn-hozzáféréshez létrehozott szerepkört.
5. A Roles Summary (Szerepkörök összefoglalása) lapon kattintással másolja a **Role ARN** (Szerepkör ARN-je) értékét. Jegyezze fel a szerepkör ARN-jét a későbbi lépésekhez.

### <a name="create-an-s3-bucket"></a>S3-gyűjtő létrehozása

Az S3-gyűjtőben részletes számlázási adatokat tárolhat.

1. Jelentkezzen be az AWS-konzolba a [https://console.aws.amazon.com](https://console.aws.amazon.com) webhelyen, és válassza a **Services** (Szolgáltatások) lehetőséget.
2. A szolgáltatások keresésére szolgáló mezőbe írja be az *S3* kifejezést, majd válassza az **S3** lehetőséget.
3. Az Amazon S3 lapon kattintson a **Create bucket** (Gyűjtő létrehozása) gombra.
4. A Create bucket (Gyűjtő létrehozása) varázslóban, válassza ki a gyűjtő nevét és régióját, majd kattintson a **Next** (Tovább) elemre.  
    ![példaadatok a Create bucket (Gyűjtő létrehozása) lapon](./media/connect-aws-account/create-bucket.png)
5. A **Set properties** (Tulajdonságok beállítása) lapon fogadja el az alapértelmezett értékeket, majd kattintson a **Next** (Tovább) elemre.
6. Az Áttekintés lapon kattintson a **Create bucket** (Gyűjtő létrehozása) elemre. Ekkor megjelenik a gyűjtőlista.
7. Kattintson a létrehozott gyűjtőre, majd az **Permissions** (Engedélyek) lapra, és végül válassza a **Bucket Policy** (Gyűjtőszabályzat) lehetőséget. Megnyílik a gyűjtőszabályzat szerkesztője.
8. Másolja ki, majd illessze be a következő JSON-példát a Bucket policy editorba (a gyűjtőszabályzat szerkesztőjébe).
   - A `<BillingBucketName>` helyére írja be saját S3-gyűjtője nevét.
   - A `<ReadOnlyUserOrRole>` helyére írja be a korábban kimásolt szerepkört vagy felhasználói ARN-t.

   ```json
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
    ![a Bucket policy editorban kattintson a Save (Mentés) gombra](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Az AWS-számlázási jelentéseinek engedélyezése

A S3-gyűjtő létrehozása és konfigurálása után lépjen az AWS-konzol [Billing Preferences](https://console.aws.amazon.com/billing/home?#/preference) (Számlázási beállítások) területére.

1. A Preferences (Beállítások) lapon válassza a **Receive Billing Reports** (Számlázási jelentések fogadása) lehetőséget.
2. A **Receive Billing Reports** (Számlázási jelentések fogadása) területen, adja meg a létrehozott gyűjtő nevét, majd kattintson a **Verify** (Ellenőrzés) elemre.  
3. Válassza ki mind a négy jelentésrészletességi beállítást, majd kattintson a **Save preferences** (Beállítások mentése) elemre.  
    ![a részletesség kiválasztása a jelentések engedélyezéséhez](./media/connect-aws-account/enable-reports.png)

A Cloudyn részletes számlázási adatokat kér le az S3-gyűjtőből, és a részletes számlázás engedélyezése után feltölti a jelentéseket. Akár 24 órát is igénybe vehet, amíg a részletes számlázási adatok megjelennek a Cloudyn-konzolon. Ha elérhetők a részletes számlázási adatok, a fiók összevonás állapota **Consolidated** (Összevont) lesz. A fiók állapota **Completed** (Befejezve) lesz.

![az összevonás állapota az AWS Accounts (AWS-fiókok) lapon](./media/connect-aws-account/consolidated-status.png)

Néhány optimalizálási jelentéshez szükség lehet néhány napnyi adatra, hogy összegyűljön a pontos javaslatokhoz szükséges mintaadat-mennyiség.

## <a name="next-steps"></a>További lépések

- Ha bővebb információra van szüksége a Cloudynről, lépjen tovább a Cloudyn [A használat és a költségek áttekintése](tutorial-review-usage.md) című oktatóanyagára.
