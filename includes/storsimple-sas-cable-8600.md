<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>Csatolása a SAS-kábel
1. Azonosítsa az elsődleges és a EBOD mindegyikéből. A két házak pedig a megfelelő hátsó síkok alapján azonosíthatók. Tekintse meg az alábbi képen útmutatót. 
   
    ![Biztonsági elsődleges vezérlősík és EBOD ház](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Biztonsági másolatot az elsődleges és a EBOD ház ábrázolása**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elsődleges ház |
   | 2 |EBOD ház |
2. Keresse meg a sorozatszámok az elsődleges és a EBOD mindegyikéből. A sorozatszám címkéje a hátsó fülön az egyes ház rögzítettek. A sorozatszámok mindkét ház azonosnak kell lennie. [Forduljon a Microsoft Support](../articles/storsimple/storsimple-contact-microsoft-support.md) azonnal, ha a sorozatszám nem egyezik. Tekintse meg a következő ábra a sorozatszám megkereséséhez.
   
    ![Hátsó ház sorozatszám megjelenítő ábrázolása](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Sorozatszám színű ragasztócsíkon helye**
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Az a ház EAR |
3. A megadott SAS-kábel segítségével csatlakozzon a EBOD ház az elsődleges ház az alábbiak szerint:
   
   1. Azonosítsa az elsődleges ház és a EBOD ház négy SAS-porttal. A SAS-porttal címkézve EBOD a elsődleges tárolóeszközön, és meg port A a EBOD tárolóeszközön, ahogy az alábbi ábrán látható, kábelek SAS.
   2. A megadott SAS-kábel segítségével csatlakozzon a EBOD port a port azonosítójához.
   3. Vezérlő 0 EBOD portját a port a 0 EBOD tartományvezérlőn kell csatlakoztatni. 1. vezérlő EBOD portját a port a EBOD tartományvezérlőn 1 kell csatlakoztatni. Tekintse meg az alábbi ábrán látható útmutatót. 
      
      ![Az eszköz kábelek SAS](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **SAS-kábelek**
      
      | Címke | Leírás |
      |:--- |:--- |
      | A |Elsődleges ház |
      | B |EBOD ház |
      | 1 |A vezérlő 0 |
      | 2 |1. vezérlő |
      | 3 |EBOD vezérlő 0 |
      | 4 |1. EBOD vezérlő |
      | 5, 6 |SAS-portot az elsődleges ház (címkézett EBOD) |
      | 7, 8 |SAS-portot a EBOD ház (Port A) |

