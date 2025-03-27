#Wack a mole
##Vakondok megjelenítése
Random 1-5 másodpercenként megjelenítünk egy vakondot
A vakond egy kép lesz, ami egy fix adott helyen jelenik meg
Ha a játékos a vakondra kattint pontot kap ,ha nem találjuk el időben akkor a vakond eltűnik és jön egy új
**Csinálunk egy while függvényt amibe a feltétel az lesz, hogy addig menjen amíg az időzítő fut
**Egy timerrbe randomizálunk egy számot 1-5 között, miután megjelenik az adott 5 hely egyikén egy button amiben egy image, ami szintén randomizálva lesz hogy hol
**Elindul mégegy timer amikor a vakond megjelenik szintén 1-5 másodperc között, hogyha lejár vagy rá kattintunk eltűnik a kép, szituációtól függően növeljük a pontot

##Pontszám
Lesz egy pontszám az egyik felső sarokban ahol a vakondok eltalált számát írjuk
A pontszám felé pedig egy rekord ami mutatja az eddigi legnagyobb elért pontszámot
**A pontszám és a rekord kettő TextBlock lesz amit minden sikeres vakondra kattintás után növelünk
**A rekord hasonló képpen fog működni mint a pontszám, annyi eltéréssel hogy akkor adjuk hozzá a pontot ha pontszám > rekord

##Időzítő
A pontszám alá kell egy időzítő, ami mutatja hogy meddig tart a játék
Ha lejár az idő a játék véget ér és középen megjelenik a pontszám nagyba.
**Csinálunk mondjuk egy 1 perces timert ami az előbb említett while függvény feltétele lesz
**Miután a timer letelik és a whileból kilépünk, vagy a meglévő pontszámot és rekordot formázzuk meg hogy középen legyen és nagyobba,
**vagy egy előre leformázott textblocknak fogjuk az oppacityjét, vagy transparencyét változtatni, hogy csak a while után lehessen látható.
##Újrakezdés
Középen a pontszám alatt egy újrakezdés gombot csinálunk ami kiürít minden adatot, kivéve a rekordot
**Csinálunk egy buttont ami végig ott lesz a képernyő közepén egy adott helyen, csak láthatatlan lesz, illetve untargetable is lesz.


