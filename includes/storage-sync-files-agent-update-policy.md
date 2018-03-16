Az Azure fájlszinkronizálás agent frissítése rendszeresen új funkciókkal és problémák megoldására. Azt javasoljuk, hogy azok elérhetők az Azure fájlszinkronizálás ügynök le a frissítéseket a Microsoft Update konfigurálása.

#### <a name="major-vs-minor-agent-versions"></a>Nagyobb vagy kisebb ügynökverziók
* Fő ügynökverziók gyakran új szolgáltatásokat tartalmazhat, és egyre több verziószáma első része lehet. Például: *2.\*.\**
* Kisebb ügynökverziók "javítások" is nevezik, és gyakrabban Főverziók kiadásakor. Hibajavításokat tartalmaz, és kisebb fejlesztései, de nincs tervben új szolgáltatások gyakran tartalmaznak. Például:  *\*.3.\**

#### <a name="upgrade-paths"></a>Frissítési útvonalak
Három jóváhagyott és tesztelt módon Azure fájlszinkronizálás ügynök frissítéseinek telepítéséhez. Ezek a frissítő elérési utak a fő és verziók.
1. **(Ajánlott) Konfigurálja a Microsoft Update használatával automatikusan letöltse és telepítse az ügynökök frissítésével.**  
    Mindig ajánlott minden Azure fájlszinkronizálás frissítése a legújabb javításokkal elérésére, hogy az a kiszolgálóügynök véve. A Microsoft Update teszi ezt a folyamatot zökkenőmentes, automatikusan frissítések letöltése és telepítése az Ön által.
2. **Javítás egy meglévő Azure fájl Sync-ügynök a Microsoft Update javítófájl vagy egy végrehajtható .msp használatával. A legújabb Azure fájlszinkronizálás csomag letölthető a [Microsoft Update katalógus](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Egy végrehajtható .msp futtató ugyanazzal a módszerrel a korábbi frissítési útvonalat a Microsoft Update automatikusan használt frissíti az Azure fájlszinkronizálás telepítése. A Microsoft Update javítás alkalmazásakor frissítését akkor fogja elvégezni egy helyszíni Azure fájlszinkronizálás telepítésének.
3. **Töltse le a legújabb Azure fájlszinkronizálás ügynök telepítőjét a [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). A telepítő letöltési egy Microsoft Installer-csomag, vagy egy végrehajtható .msi.**  
    Egy meglévő Azure fájl Sync-ügynök telepítése, frissítése eltávolítja a régebbi verziót, és majd telepítse a legújabb verzióra a letöltött telepítőt. A kiszolgáló regisztrálása, szinkronizálási csoportok és egyéb beállításokat a Azure fájlszinkronizálás Installer karbantartása.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Biztosítja, hogy az ügynök életciklusát, és módosítsa a felügyeleti
Az Azure fájlszinkronizálás egy felhőalapú szolgáltatás, amely lehetővé teszi, hogy folyamatosan bevezetése új szolgáltatásait és funkcióit. Ez azt jelenti, hogy egy adott Azure fájl Sync-ügynök verziója csak támogatható adott időtartamra. A központi telepítés elősegítése érdekében garantálja, hogy a következő szabályok van elegendő idő és az értesítési ügynök frissítést/frissítéseket a változáskezelési folyamatot az alkalmazásához:

- A kezdeti kiadás legalább hat hónapig fő ügynök verziókat támogatja.
- Garantáljuk legalább három hónapos fő ügynökök verzióinak támogatása között átfedés van. 
- A rendszer figyelmeztetést a regisztrált kiszolgálókat hamarosan-a-lehet lejárt ügynök legalább három hónapos lejárta előtt. Ellenőrizheti, ha egy regisztrált kiszolgáló az ügynök egy régebbi verzióját használja egy tároló szinkronizálási szolgáltatás regisztrált kiszolgálókat részében található.
- A kisebb ügynökverzió élettartama a társított főverzió van kötve. Például ha 3.0-s verziója felszabadul, ügynökök verzióinak 2. \* összes úgy lesz beállítva, együtt lejár.

> [!Note]
> Az ügynök verziójának telepítése egy lejárati figyelmeztetéssel vonatkozó figyelmeztetést jelenít meg, de sikertelen. Megpróbálja telepíteni, vagy kapcsolódjon egy lejárt ügynök verziója nem támogatott, és le lesz tiltva.