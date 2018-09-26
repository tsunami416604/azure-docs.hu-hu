<!--author=SharS last changed: 1/7/2016-->

#### <a name="to-add-a-volume-container"></a>Kötettároló hozzáadása
1. Az a **eszközök** lapon válassza ki az eszközt, kattintson rá duplán, és kattintson a **kötettárolók** fülre.
2. Kattintson a **Hozzáadás** az oldal alján. Az a **kötettároló létrehozása** párbeszédpanelen tegye a következőket:
   
   1. Adjon egy egyedi **neve** a kötettároló. Ez a név tartalmazhat egy legfeljebb 32 karakter hosszú lehet.
   2. Válassza ki a **Tárfiók** ehhez a kötettárolóhoz társítandó. Választhat egy meglévő tárfiókot a azonos előfizetésben, vagy válasszon belül **bővítése** válassza ki a storage-fiók egy másik előfizetésből. Azt is beállíthatja a storage-fiók, először jött létre, ha a szolgáltatás létrejött.
   3. Adja meg a sávszélességet **korlátlan** használja a rendelkezésre álló sávszélességet, ha vagy **egyéni** a sávszélesség-korlátozást. Egyéni sávszélesség adjon meg 1 és 1000 MB/s közötti értéket. Az ütemezés alapján sávszélességet kell kiosztania, is **sávszélességsablon kiválasztása**.
   4. Azt javasoljuk, hogy maradjon **felhőalapú tárolás titkosításának engedélyezése** kiválasztott titkosíthatja az adatokat, amelyet szeretne a felhőbe. Tiltsa le a titkosítást, csak akkor, ha meg vannak alkalmazó egyéb azt jelenti, hogy az adatok titkosításához. A titkosítási beállítást a kötettároló létrehozása után nem módosítható.
   5. Adjon meg egy **felhőalapú tárolás titkosítási kulcsa** , amely 8 – 32 karakter hosszúságú. Az eszköz a titkosított adatok hozzáféréséhez használja ezt a kulcsot. Az a **megerősítése felhőalapú tárolás titkosítási kulcsa** mezőben adja meg a felhőalapú tárolás titkosítási kulcsa újra a megerősítéshez. 
   6. Kattintson a nyílra kattintva folytassa a következő lapra.
      
      ![Sávszélességsablon 1 kötettároló létrehozása](./media/storsimple-add-volume-container/HCS_CreateVCBT1-include.png) 
3. Ha a megadott **sávszélességsablon kiválasztása**, a meglévő sávszélességsablonok a legördülő listából válassza ki. Tekintse át az ütemezési beállításokat, és kattintson a pipa ikonra ![pipa ikon](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png).
   
    ![Sávszélességsablon 2 kötettároló létrehozása](./media/storsimple-add-volume-container/HCS_CreateVCBT2-include.png) 

A kötettároló menti a rendszer, és az újonnan létrehozott kötettároló megjelenik a a **kötettároló** lapot.

