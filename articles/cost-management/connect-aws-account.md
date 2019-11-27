---
title: Csatlakozás Amazon Web Services-fiók az Azure-ban a Cloudyn |} A Microsoft Docs
description: Csatlakozás egy Amazon Web Services-fiók költség- és használati adatok megtekintéséhez a Cloudyn jelentésekben.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 676c01a26d67b395340e5b1ed2dacc6b3b824742
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219735"
---
# <a name="connect-an-amazon-web-services-account"></a>Csatlakozás Amazon Web Services-fiók

Az Amazon Web Services (AWS) fiók csatlakoztatásához a Cloudyn két lehetősége van. Az IAM-szerepkörrel, vagy egy csak olvasható IAM felhasználói fiókkal is lehet kapcsolódni. Az IAM-szerepkör ajánlott, mert lehetővé teszi, hogy meghatározott engedélyekkel a megbízható entitások hozzáférés delegálására. Az IAM-szerepkör nem igényel hosszú távú tárelérési kulcsok megoszthatja. AWS-fiók a Cloudynben a kapcsolódás után a költség- és használati adatokat a Cloudyn jelentésekben érhető el. Ez a dokumentum végigvezeti mindkét lehetőség.

Az AWS IAM-identitásokkal kapcsolatos további információkért lásd: [identitások (felhasználók, csoportok és szerepkörök)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Ezenkívül engedélyeznie az AWS részletes jelentések számlázási és a egy AWS simple storage service (S3) gyűjtőbe az információkat tárolja. Részletes számlázási jelentések címke- és erőforrás adatok óránként díjak tartalmazzák. A jelentések tárolására lehetővé teszi, hogy a Cloudyn kérheti le azokat, a gyűjtő és az információk megjelenítése a jelentésekben.


## <a name="aws-role-based-access"></a>Az AWS-szerepkörön alapuló hozzáférés

A következő szakaszok végigvezetik egy csak olvasható IAM szerepkör számára hozzáférést biztosítsunk a Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>A Cloudyn-fiók külső azonosító beszerzése

Az első lépés, hogy az egyedi kapcsolat hozzáférési kód lekérése a Cloudyn portálon. Az AWS-ben **külső azonosítóként**használatos.

1. Nyissa meg a Cloudyn-portált a Azure Portal, vagy navigáljon a [https://azure.cloudyn.comra](https://azure.cloudyn.com) , és jelentkezzen be.
2. Kattintson a fogaskerék jelre, majd válassza a **Cloud accounts (Felhőbeli fiókok**) lehetőséget.
3. A fiókok kezelése lapon válassza az **AWS-fiókok** fület, majd kattintson az **új + hozzáadása**lehetőségre.
4. Az **AWS-fiók hozzáadása** párbeszédpanelen másolja át a **külső azonosítót** , és mentse az AWS szerepkör-létrehozási lépések értékét a következő szakaszban. A külső azonosító egyediségét a fiókjához. A következő képen a külső azonosító a _contoso_ , amelyet egy szám követ. Az azonosító különbözik.  
    ![külső azonosítót az AWS-fiók hozzáadása mezőbe](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Az AWS csak olvasható szerepkör alapú hozzáférés hozzáadása

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com/iam/home és válassza a **szerepkörök**lehetőséget.
2. Kattintson a **szerepkör létrehozása** lehetőségre, majd válasszon **egy másik AWS-fiókot**.
3. A **fiók azonosítója** mezőben illessze be a `432263259397`. A Fiókazonosító az AWS által a Cloudyn-szolgáltatáshoz rendelt Cloudyn gyűjtő fiók. Használhatja a pontos Fiókazonosító látható.
4. A **Beállítások**mellett válassza a **külső azonosító megkövetelése**lehetőséget. Illessze be a korábban a Cloudyn **külső azonosító** mezőjéből másolt egyedi értéket. Ezután kattintson a **Tovább gombra: engedélyek**.  
    ![illessze be a külső azonosítót a Cloudyn elemre a szerepkör létrehozása oldalon](./media/connect-aws-account/create-role01.png)
5. Az **engedélyek csatolása**területen a **házirend típusának** szűrése mezőbe írja be a `ReadOnlyAccess`kifejezést, válassza a **ReadOnlyAccess**, majd a **Tovább: felülvizsgálat**lehetőséget.  
    ![válassza a csak olvasási hozzáférés elemet a házirendek nevei listájában](./media/connect-aws-account/readonlyaccess.png)
6. Az Áttekintés lapon győződjön meg arról, hogy a beállítások helyesek, és adjon meg egy **szerepkör-nevet**. Például: *Azure-Cost-kez*. Adja meg a **szerepkör leírását**. Például a _Cloudyn szerepkör-hozzárendelését_, majd kattintson a **szerepkör létrehozása**lehetőségre.
7. A **szerepkörök** listájában kattintson a létrehozott szerepkörre, és másolja az **ARN (szerepkör** ) értéket az összefoglalás lapról. A szerepkör információ (Amazon erőforrás neve) értéke is regisztrálhatja a konfigurációt, a Cloudyn később használni.  
    ![másolja az ARN szerepkört az összefoglalás lapról](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>A Cloudyn AWS IAM szerepköralapú hozzáférés konfigurálása

1. Nyissa meg a Cloudyn-portált a Azure Portal, vagy navigáljon a https://azure.cloudyn.com/ra, és jelentkezzen be.
2. Kattintson a fogaskerék jelre, majd válassza a **Cloud accounts (Felhőbeli fiókok**) lehetőséget.
3. A fiókok kezelése lapon válassza az **AWS-fiókok** fület, majd kattintson az **új + hozzáadása**lehetőségre.
4. A **fiók neve**mezőben adja meg a fiók nevét.
5. A **hozzáférés típusa**mellett válassza a **iam szerepkör**lehetőséget.
6. Illessze be a korábban másolt értéket a **role ARN** mezőbe, majd kattintson a **Save (Mentés**) gombra.  
    ![illessze be az ARN szerepkört az AWS-fiók hozzáadása mezőbe](./media/connect-aws-account/add-aws-account-box.png)


AWS-fiók fiókok listája jelenik meg. A felsorolt **tulajdonosi azonosító** megfelel a szerepkör ARN értékének. A **fiók állapotának** zöld pipa szimbólummal kell rendelkeznie, amely azt jelzi, hogy a Cloudyn HOZZÁFÉRHET az AWS-fiókhoz. Amíg nem engedélyezi a részletes AWS-számlázást, a konszolidációs állapot **önállóként**jelenik meg.

![A fiókok kezelése lapon látható az AWS-fiók állapota](./media/connect-aws-account/aws-account-status01.png)

A Cloudyn elindítja az adatok gyűjtése és jelentések feltöltése. Ezután [engedélyezze a részletes AWS-számlázást](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS – felhasználó-alapú hozzáférés

A következő szakaszok végigvezetik egy csak olvasható hozzáférést biztosítsunk a Cloudyn-felhasználó létrehozása.

### <a name="add-aws-read-only-user-based-access"></a>Az AWS írásvédett felhasználó-alapú hozzáférés hozzáadása

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com/iam/home és válassza a **felhasználók**lehetőséget.
2. Kattintson a **felhasználó hozzáadása**elemre.
3. A **Felhasználónév** mezőbe írja be a felhasználónevet.
4. A **hozzáférés típusa**beállításnál válassza a **programozott hozzáférés** lehetőséget, majd kattintson a **Tovább gombra: engedélyek**.  
    ![adjon meg egy felhasználónevet a felhasználó hozzáadása lapon](./media/connect-aws-account/add-user01.png)
5. Az engedélyek beállításnál válassza a **meglévő szabályzatok közvetlen csatolása**lehetőséget.
6. Az **engedélyek csatolása**területen a **házirend típusának** szűrése mezőbe írja be a `ReadOnlyAccess`kifejezést, válassza a **ReadOnlyAccess**lehetőséget, majd kattintson a **Tovább gombra: Áttekintés**.  
    ![válassza a ReadOnlyAccess lehetőséget a felhasználó engedélyeinek beállításához](./media/connect-aws-account/set-permission-for-user.png)
7. Az Áttekintés lapon győződjön meg arról, hogy helyesek a megfelelő beállítások, majd kattintson a **felhasználó létrehozása**elemre.
8. A teljes lapon jelenik meg a hozzáférési kulcs azonosítója és kulcsa hozzáférési kulccsal. Ezen információk használatával a Cloudyn regisztrációs konfigurálása.
9. Kattintson a **download. csv** fájlra, és mentse a hitelesítő adatokat tartalmazó. csv fájlt egy biztonságos helyre.  
    ![a hitelesítő adatok mentéséhez kattintson a Download. csv fájlra](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>A Cloudyn AWS IAM felhasználóalapú hozzáférés konfigurálása

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com/ webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék jelre, majd válassza a **Cloud accounts (Felhőbeli fiókok**) lehetőséget.
3. A fiókok kezelése lapon válassza az **AWS-fiókok** fület, majd kattintson az **új + hozzáadása**lehetőségre.
4. A **fiók neve**mezőbe írja be a fiók nevét.
5. A **hozzáférés típusa**mellett válassza a **iam felhasználó**elemet.
6. A **hozzáférési kulcsban**illessze be a **hozzáférési kulcs azonosítójának** értékét a hitelesítő adatok. csv fájlból.
7. A **titkos kulcs**mezőben illessze be a **titkos elérési kulcs** értékét a hitelesítő adatok. csv fájlból, majd kattintson a **Mentés**gombra.  

AWS-fiók fiókok listája jelenik meg. A **fiók állapotának** zöld pipa jellel kell rendelkeznie.

A Cloudyn elindítja az adatok gyűjtése és jelentések feltöltése. Ezután [engedélyezze a részletes AWS-számlázást](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Részletes AWS Számlázás engedélyezése

Az alábbi lépések segítségével az AWS-szerepkör információ beolvasása. A szerepkör információ segítségével egy számlázási gyűjtőhöz olvasási engedélyeket ad.

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com és válassza a **szolgáltatások**lehetőséget.
2. A szolgáltatás keresési mezőjében írja be a következőt: *iam*, és válassza ezt a lehetőséget.
3. Válassza ki a **szerepkörök** elemet a bal oldali menüben.
4. A szerepkörök listájában válassza ki a Cloudyn-hozzáférést létrehozott szerepkör.
5. A Szerepkörök összegzése lapon kattintson ide az **ARN szerepkör**másolásához. Tartsa meg a szerepkör információ hasznos a későbbi lépésekben.

### <a name="create-an-s3-bucket"></a>Hozzon létre egy S3 gyűjtő

Létrehozhat egy S3 gyűjtőt részletes számlázási információk tárolására.

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com és válassza a **szolgáltatások**lehetőséget.
2. A szolgáltatás keresési mezőjébe írja be az *S3*kifejezést, majd válassza az **S3**elemet.
3. Az Amazon S3 lapon kattintson a **create Bucket (gyűjtő létrehozása**) elemre.
4. A gyűjtő létrehozása varázslóban válassza ki a gyűjtő nevét és a régiót, majd kattintson a **tovább**gombra.  
    ![példa a Create Bucket (gyűjtő létrehozása) lapra](./media/connect-aws-account/create-bucket.png)
5. A **készlet tulajdonságai** lapon tartsa meg az alapértelmezett értékeket, majd kattintson a **tovább**gombra.
6. Az Áttekintés lapon kattintson a **gyűjtő létrehozása**elemre. A gyűjtő lista jelenik meg.
7. Kattintson a létrehozott gyűjtőre, és válassza az **engedélyek** fület, majd válassza a **gyűjtő házirend**elemet. A gyűjtő Helyicsoportházirend-szerkesztő megnyitása.
8. Másolja az alábbi példa JSON, és illessze be a gyűjtő Helyicsoportházirend-szerkesztő.
   - Cserélje le a `<BillingBucketName>`t az S3 gyűjtő nevére.
   - Cserélje le a `<ReadOnlyUserOrRole>`t arra a szerepkörre vagy felhasználói ARN-ra, amelyet korábban már másolt.

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
    ![kattintson a Mentés gombra a gyűjtő házirend-szerkesztőjében](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>Az AWS számlázási jelentések engedélyezése

Az S3-gyűjtő létrehozása és konfigurálása után navigáljon a [számlázási beállítások](https://console.aws.amazon.com/billing/home?#/preference) pontra az AWS-konzolon.

1. A beállítások lapon válassza a **Számlázási jelentések fogadása**lehetőséget.
2. A **Számlázási jelentések fogadása**területen adja meg a létrehozott gyűjtő nevét, majd kattintson az **ellenőrzés**gombra.  
3. Válassza ki mind a négy jelentés részletességi beállításait, majd kattintson a **Beállítások mentése**lehetőségre.  
    ![a jelentések engedélyezéséhez válassza a részletesség lehetőséget](./media/connect-aws-account/enable-reports.png)

A Cloudyn részletes számlázási adatait kérdezi le az S3 gyűjtőt, és feltölti a jelentések, részletes számlázási engedélyezése után. Mindaddig, amíg a Cloudyn-konzolon megjelenik a részletes számlázási adatok 24 órát is igénybe vehet. Ha részletes számlázási információk állnak rendelkezésre, a fiók konszolidációs állapota **összevonva**jelenik meg. A fiók állapota **befejezettként**jelenik meg.

![az AWS-fiókok lapon látható konszolidálási állapota](./media/connect-aws-account/consolidated-status.png)

Néhány nap adatait egy megfelelő minta adatméret pontos javaslatokat beolvasni az optimalizálási jelentések némelyike lehet szükség.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni a Cloudyn-ről, folytassa a használati és Cloudyn kapcsolatos oktatóanyag [áttekintésével](tutorial-review-usage.md) .
