---
Cím: Adatokat importálhat fájl titleSuffix: Azure Machine Learning Studio description: Útmutató a betanítási adatok fájlok feltöltéséhez a merevlemez-meghajtóról az Azure Machine Learning Studióban. Ez létrehoz egy adatkészlet-modul a munkaterületen.
szolgáltatások: gépi tanulási ms.service: gépi tanulási ms.component: studio ms.topic: cikk

author: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 03/20/2017
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-azure-machine-learning-studio"></a>Betanítási adatok importálása egy fájlból a merevlemezen, az Azure Machine Learning studióba

Ismerje meg, hogyan tölthet fel egy adatfájlt a merevlemez-meghajtóról a betanítási adatok az Azure Machine Learning Studióban adatokként. Importálja a fájlt, van egy használatra kész adatkészlet modul munkaterületét.

## <a name="steps-to-import-data-from-a-local-file"></a>Adatokat importálhat egy helyi fájlból lépései
Adatokat importálhat a helyi merevlemezen, tegye a következőket:

1. Kattintson a **+ új** a Machine Learning Studio ablakának alján.
2. Válassza ki **ADATKÉSZLET** és **helyi FÁJLBÓL**.
3. Az a **töltse fel egy új adatkészlet** párbeszédpanelen tallózással keresse meg a feltölteni kívánt fájlt
4. Adjon meg egy nevet, az adattípus azonosításához, és igény szerint adjon meg egy leírást. Leírását javasolt – lehetővé teszi bármely jellemzőinek a jövőben az adatok használatakor vegye figyelembe az adatok rögzítéséhez.
5. A jelölőnégyzet **az új verziója, amelyet egy meglévő adatkészlet** lehetővé teszi, hogy egy meglévő adatkészlet új adatokkal frissülnek. Kattintson a jelölőnégyzetre, és írja be egy meglévő adatkészlet nevét.

![Töltse fel egy új adatkészlet](./media/import-data-from-local-file/upload-dataset.png)

Feltöltésekor megjelenik egy üzenet, hogy a fájl feltöltése folyamatban van. Feltöltés ideje az adatok méretétől és a szolgáltatás a kapcsolat sebességétől függ. Ha tudja, hogy a fájl egy hosszú időt vesz igénybe, további tevékenység következik, Machine Learning Studio belül megteheti, várakozás közben. Azonban a böngésző bezárásával hatására az adatok feltöltése sikertelen.

## <a name="dataset-module-is-ready-for-use"></a>Adatkészlet modul készen áll a használatra
Az adatok feltöltését követően egy adatkészlet modulban tárolja, és bármely a kísérletvászonra a munkaterületén érhető el.

Kísérlet szerkesztésekor találja a korábban létrehozott adatkészletek a **saját adatkészletek** alatt a **mentett adatkészletek** a modulpaletta listájában. Húzza át, és dobja el az adatkészletet a kísérlet vászonra, ha meg szeretné használni az adatkészlet további elemzés és gépi tanulás.
