# MAP

kruh.h
//
// Created by map on 27. 9. 2021.
//
#ifndef PRVY_KRUH_H
#define PRVY_KRUH_H
#include <iostream>

class Kruh
{
    //rodicovska trieda od ktorej odvodime triedy chyb
    class Chyba
    {
    protected: //chraneny clen, potomkovia maju k nemu pristup ako k verejnemu, ostatne triedy ako k privatnemu
        const char * msg;
        int kodChyby;
    public:
        Chyba(const char *sprava):msg(sprava){};
        virtual void getMsg() const {std::cout<<msg<<std::endl;}; //virtual pridavam vtedy, ak viem, ze v triede potomka budem chciet tuto triedu prepisat
        int vratKodChyby(){return kodChyby;};
    };
    //vlozenie triedy sluziace na vytvorenie vynimiek


public: class streamError:public Chyba
    {
    public:
        streamError(const char *sprava): Chyba(sprava){};
    };


    class noNumber:public Chyba //trieda je verejne odvodena od triedy Chyba
    {
    private:
        const char * mojaMsg;
     public:
        noNumber(const char * sprava, const char * mojaSprava):Chyba(sprava){mojaMsg=mojaSprava;};
        const char * getMojaMsg(){return mojaMsg;};
        void getMsg()const {std::cout<<msg<<mojaMsg<<std::endl;};
    };

    class chybaNula:public Chyba
    {
    public:
        chybaNula(const char * sprava):Chyba(sprava){};
    };

    class chybaZaporne:public Chyba
    {
    public:
        chybaZaporne(const char * sprava):Chyba(sprava){};
    };

private:
    int polomer;
    int pocitadlo;
public:
    ~Kruh(); //destruktor
    Kruh(); //Vypyta si polomer vznikajceho kruhu
    explicit Kruh(int r); //Vytvori kruh a automaticky jeho polomer nastavi na hodnotu r; explicit --) zakaze konverziu cisla na kruh
    //char farba;
    int getPolomer() const {return polomer;}; //vyrobil som z funkcie inline metodu, teda metoda, kedy je telo priamo uvedene v definicii jej triedy
    void setPolomer(int r){polomer=r;}; //je vhodne vyuzit tuto inline metodu len ked je na jeden riadok
    float getObvod() const;
    float getObsah() const;

    //pretazovanie operatorov
    operator int () const; //konverzia objektu Kruh na cele cislo, je bez navratovej hodnoty aj parametra
    operator float() const; //konverzia objektu Kruh na float, ktory je obvodom kruhu
    Kruh operator+(const Kruh &other) const;
    bool operator<(const Kruh &other) const;
    Kruh operator-(const Kruh &other) const;

    Kruh operator+(int cislo) const;//pripocita ku kruhu cislo
    friend Kruh operator+(int cislo, const Kruh &other);//priatelska funkcia, ktora k cislu pripocita kruh, metoda ktoru nevolame na objekte, ale napriek tomu jej umoznim priamy pristup k privatnym premennym objektu

    Kruh operator*(int cislo) const;
    friend Kruh operator*(int cislo, const Kruh &other); //priatelska funkcia vynasobi cislo kruhom

    Kruh operator-(int cislo) const;
    const Kruh& operator++();
    Kruh & operator++(int nepouzijem);//Parameter sa nepouzije, ale znamena, ze sa pretazuje postfix
    const Kruh& operator--();
    Kruh & operator--(int nepouzijem);
    const Kruh& operator+=(int cislo);
    const Kruh& operator-=(int cislo);
    friend std::ostream & operator<<(std::ostream &os, const Kruh &mojKruh); //pretazenie operatoru na vystup
    //friend std::istream & operator<< __attribute__((unused))

    //koniec pretazovania operatorov


    /*Kruh spocitajKruhy(Kruh other) const;
    Kruh spocitajKruhy(const Kruh *other) const;
    Kruh spocitajKruhy() const; //zalozi novy kruh, vypta si jeho polomer a spocita ho s volajucim
    Kruh odcitajKruhy(Kruh other) const;    //ak bude polomer mensi tak vrati 1
    Kruh odcitajKruhy(const Kruh * other) const;
    Kruh odcitajKruhy() const;

    bool jeMensi(const Kruh *other);*/
    //static const float PI;
    static constexpr float PI=3.14; // nova metoda pre c++ (je jednoduchsia a rychlejsia)
    static void vymenKruhy(Kruh *prvy, Kruh *druhy);
    static void vymenKruhy(Kruh &prvy, Kruh &druhy);
    static void generujPoleKruhov(Kruh *pole, int pocet);
    static void vypisPoleKruhov (const Kruh *pole, int pocet);
    static Kruh getMaxKruh(const Kruh *pole, int pocet);
    static Kruh* getMaxKruh(Kruh *pole, int pocet);
    static void utriedPoleKruhov(Kruh *pole, int pocet);
    static int cmp (const void * a, const void * b);  //cmp sa oznacuje porovnavacia funkcia, ale standardne sa oznacuje akokolvek
    static int cmpstable(const void * a, const void * b);
    static int pocetKruhov;
    static int getInt(bool nula=true, bool zaporne=true);
    static int generujSuborKruhov(const char *nazov, int kolko);
    static Kruh * precitajSuborKruhov(const char *nazov, int kolko);




};
//const float PI = 3.14;


#endif //PRVY_KRUH_H



















kruh.cpp
//
// Created by map on 27. 9. 2021.
//

#include <iostream>
#include <time.h>
#include "kruh.h"
#include <f.stream>

int Kruh::pocetKruhov=0;


Kruh::Kruh()
{
    pocitadlo =++pocetKruhov;
    polomer = 0;
}

Kruh::Kruh(int r):polomer(r) //keby mam parametrov viac, tak staci dat za polomer(r) ciarku a mozem pokracovat //toto nazyvame inicializacny zoznam premennych
{
    //this->polomer=r; ked napisem --) polomer (r) tak toto tu uz nemusi byt
}

Kruh Kruh::odcitajKruhy(Kruh other) const {
    int polomer;
    std::cout<<"Zadaj polomer:";
    std::cin>>polomer;
    return {r: this->polomer-polomer};

}

void Kruh::vymenKruhy(Kruh *prvy, Kruh *druhy)
{
    Kruh Pomocny(0);
    Pomocny=*prvy;
    *prvy=*druhy;
    *druhy=Pomocny;
}

void Kruh::vymenKruhy(Kruh &prvy, Kruh &druhy)
{
    Kruh TMP(0);
    TMP = prvy;
    prvy=druhy;
    druhy=TMP;

}

Kruh Kruh::operator+(const Kruh &other) const
{
    return {polomer+other.polomer};
    //return Kruh(polomer+other.polomer) --) alternativa

}

bool Kruh::operator<(const Kruh &other) const {
    return polomer<other.polomer;
}

const Kruh &Kruh::operator++()
{
    ++polomer;
    return (*this);
}

const Kruh &Kruh::operator++(int nepouzijem)
{
    Kruh Tmp = (*this);
    ++polomer;
    return Tmp;

}

const Kruh &Kruh::operator--()
{
    polomer= (polomer-1<=0)?1:polomer-1;
    return (*this);
}

Kruh operator+(int cislo, const Kruh &other) {
    return other+cislo;
}

std::ostream &operator<<(std::ostream &os, const Kruh &mojKruh)
{
    os<<"Kruh ma polomer"<<mojKruh.polomer<<std::endl;
    return os;
}

void Kruh::generujPoleKruhov(Kruh *pole, int pocet)
{
    std::srand(time(NULL));
    for(int i=0;i<pocet;i++)
    {
        (pole+i)->polomer=std::rand()%99+1;
    }
}

Kruh Kruh::getMaxKruh(const Kruh *pole, int pocet)
{
    Kruh * kde = pole;
            for(int i=1;i<pocet;++i)
            {
                kde=((*kde)<*(pole+i))?(pole+i):kde;
            }
    return kde;
}

Kruh *Kruh::getMaxKruh(Kruh *pole, int pocet) {
    Kruh Max = pole[0]
    for(int i=1;i<pocet;++i)
    {
        Max=(Max<pole[i])?pole[i]:Max;
    }

    return Max;
}

Kruh::operator int() const
{
    return polomer;
}

Kruh::operator float() const {
    return PI*2*polomer;
}

int Kruh::cmp(const void *a, const void *b)
{
    Kruh *prvy;(Kruh *)a; //pretypujeme pointer na void na konkretny pointer ktory potrebujeme, u nas Kruh
    Kruh *druhy; = (Kruh *)b;

    return prvy->polomer-druhy->polomer;
}

void Kruh::utriedPoleKruhov(Kruh *pole, int pocet)
{
    std::qsort((Kruh*)pole,pocet,sizeoff(Kruh),cmp);
}

int Kruh::cmpstable(const void *a, const void *b)
{
    Kruh *prvy;(Kruh *)a; //pretypujeme pointer na void na konkretny pointer ktory potrebujeme, u nas Kruh
    Kruh *druhy; = (Kruh *)b;
    int rozdiel =prvy->polomer-druhy->polomer;

    return (rozdiel==0)?prvy->pocitadlo-druhy->pocitadlo:rozdiel;
}

int Kruh::getInt(bool nula, bool zaporne)
{
    int tmp;
    while(true)
    {
        try
        {
            std::cout<<"Zadaj cele cislo:";
            if(!std::cin>>tmp) //ak nebolo scitanie uspesne t.j. mnebolo zadane korektne cele cislo, nastavi sa chybovy bit
            {
                throw Kruh::noNumber("Nebolo zadane cislo!"); //vyhodime vynimku a posleme spravu konstuktoru

            }
            if(nula==false && tmp == 0)//nula nebola povolena ale bola zadana
            {
                throw Kruh::chybaNula("Nula nie je povolena!");
            }
            if(zaporne==false && tmp<0)
            {
                throw Kruh::chybaZaporne("Zaporne cislo nie je povolene!");
            }

        }
        catch (const Kruh::noNumber & ex) //zachytime vynimku ak nebolo zadane cislo, objektu rodica sa moze priradit odkaz na objekt potomka
                                            //kompilator potom vzdy zavola spravnu metodu (musi byt oznacena ako virtualna) podla typu odkazu, ak nie podla typu objektu
        {
            std::cin.clear(); //vymazeme chybovy bit nastaveny v objekte cin neuspesnym citanim
            std::cin.ignore(1000,\n); //vycisti vyrovnavaciu pamat klavesnice od zvysku zadaneho vstupu
            ex.getMsg(); //vypiseme spravu
            continue;
        }
        catch (const Kruh::chybaNula & ex)
        {
            std::cin.clear(); //vymazeme chybovy bit nastaveny v objekte cin neuspesnym citanim
            std::cin.ignore(1000,\n); //vycisti vyrovnavaciu pamat klavesnice od zvysku zadaneho vstupu
            ex.getMsg(); //vypiseme spravu
            continue;
        }
    }

    return 0;
}

int Kruh::generujSuborKruhov(const char *nazov, int kolko)
int i;
int pocitadlo;
{
    try
    {
        if(!fout.is_open()) {
throw Kruh::streamError("Nepodarilo sa otvorit subor na citanie: ");
        }
      throw Kruh::streamError("Nepodarilo sa otvorit subor na citanie: ");
        }

    for(i=0;i<kolko;i++){
        fout<<rand() %50<<;
    }

    fout.close();

    }
    catch (const Kruh:.streamError ex){
        ex.getMsg();
        return 1;
    }

    return 0;
}

Kruh *Kruh::precitajSuborKruhov(const char *nazov, int kolko)
Kruh *pole;
try{
    pole = newKruh(kolko)
}
catch(std::bad_alloc & ex){
    std::cout<<"Nepodarilo sa alokovat pamat!: "
}

int i = 0;
std::ifstream fin;
fin.open(nazov)

try {
if(!fin.

is_open()

) {
throw Kruh::streamError("Nepodarilo sa otvorit subor na citanie: ");
}
while(fin>> i)
{
(pole+i) ->
polomer = i;
}
fin.

close()
}

catch (const Kruh::streamError & ex){
    ex.getMsg();
    return NULL;
}

return pole;
}
















main.cpp
#include <iostream>
#include "kruh.h"
#include <fstream> //na pracu so subormi
using std::cout;
using std::cin;
using std::endl;
using std::ifstream; //sluzi na vstup zo suboru
using std::ofstream; //sluzi na vystup zo suboru

int main()
{
    //citanie zo suboru
    ifstream fin; //vytvoreny objekt pre citanie zo suboru
    fin.open("citaj.txt"); //inicializujeme objekt konkretnym suborom
    //ifstream fin("citaj.txt"); alternativny sposob otvorenie suboru pomocou konstruktoru
    ofstream fout;
    fout.open("zapis.txt");

    try 
    {
     if(!fin.is_open()) //otestujem otvorenie suboru, ak sa nepodarilo otvorit, vyhodim vynimku   
     {
         throw Kruh::streamError("Nepodarilo sa otvorit subor na citanie!");
     }
     Kruh K(0); //objekt do ktoreho budeme citat jednotlive kruhy
        if(!fout.is_open()) //otestujem otvorenie na zapis, ak sa nepodarilo otvorit, vyhodim vynimku
        {
            throw Kruh::streamError("Nepodarilo sa otvorit subor na zapis!");
        }
     while(fin>>K) //kym je citanie uspesne
     {
         cout<<K; //vypiseme precitany kruh na obrazovku
         fout<<K; //zapiseme kruh do suboru
     }
     fout.close();
     fin.close(); //uzatvorime subor
    }

    catch (const Kruh::streamError & ex) //zachytenie vynimky
    {
        ex.getMsg();
        return 1;
    }
        

    
    
    /*Kruh Prvy;
    Kruh Druhy(10);
    Kruh Treti = Kruh(5);
    Kruh Stvrty = Kruh();
    Kruh Piaty {6};
    auto cislo =10;
    auto realne =5.14;
    auto Siesty = Kruh();

    Kruh k1(5);
    Kruh k2 (10);
    Kruh::vymenKruhy(&k1,&k2);
    std::cout<<k1.getPolomer()<<" "<<k2.getPolomer();

    Kruh k1(4);
    Kruh k2 (8);
    Kruh::vymenKruhy(&:K1, &:K2);
    std::cout<<k1.getPolomer()<<" "<<k2.getPolomer();

    Kruh K1(4);
    Kruh K2{8};
    Kruh Spocitane=K1+K2;
    std::cout<<Spocitane.getPolomer();
    std::cout<<(K1<K2)<<endl;
    std::cout<<K1+10);
    std::cout<<K1*10);
    std::cout<<K1-10);
    std::cout<<K1-K2);
    ++K1;
    K2++;
    std::cout<<+K1).getPolomer()<<endl;
    std::cout<<K1;
    std::cin>>K2; 


    const int kolko = 50;
    Kruh kruhy[kolko];
    int pocet;
    //dynamicky generovane pole
    cout<<"Zadaj pocet prvkov pola:";
    cin>>pocet;
    Kruh *dynamickePole = new Kruh[pocet]; //alokacia pamate na hromade operatorom new
            if(dynamickePole==NULL)
            {
                cout<<"Nepodarilo sa alokovat pamat. Koncim!";
                exit(2); //malo by to vyzerat takto --) exit ( status: 2);
            }
            Kruh::generujPoleKruhov(dynamickePole,pocet);
            cout<<"Povodne pole:"<<endl;
            Kruh::vypisPoleKruhov(dynamickePole,pocet);
            Kruh::utriedPoleKruhov(dynamickePole,pocet);
            cout<<"Utriedene pole:"<<endl;
            Kruh::vypisPoleKruhov(dynamickePole,pocet);
            delete[] dynamickePole; //uvolnenie pamäte
    dynamickePole=0;



    Kruh::generujPoleKruhov(kruhy,kolko);
    Kruh::vypisPoleKruhov(kruhy,kolko);


    //cout<<"Maximalny kruh: "<<Kruh::getMaxKruh(kruhy,kolko);
    cout<<"Maximalny kruh: "<<*(Kruh::getMaxKruh(kruhy,kolko));
    int hodnota = Kruh(10);
    float obvod = Kruh(5);
*/
    return 0;


}
