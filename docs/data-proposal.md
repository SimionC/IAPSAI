# Dataset presentation

## OCRInvoice – Dataset facturi și chitanțe

Participanți: Filippos Schilizzi, Frîncu Luisa, Mihai Florențiu, Simion Cristina-Florentina – 406

## 1. De ce acest dataset?

**Problema:**  Introducerea manuală a liniilor de pe bonuri și facturi consumă timp și produce erori. Totalul unui bon nu arată pe ce s-au cheltuit banii, iar denumirile prescurtate de pe documente îngreunează categorisirea. OCRInvoice extrage antetul și liniile de produs, le propune utilizatorului spre verificare și le organizează pe categorii.

**La ce e bun:** Setul conține bonuri fiscale, facturi și chitanțe, cu valori în mai multe valute: euro, ron, dolari, etc., adnotate:

- la nivel de document: tip, emitent(nume, adresa, CUI),cumpărător (dacă apare - nume, adresa, CUI), dată, dată scadentă, număr, total, taxe;
- la nivel de linie: denumire, cantitate, preț unitar, total linie, cotă TVA.

**Poate fi folosit pentru:**

- evaluarea extragerii datelor de pe documentele alese;
- antrenarea și evaluarea categorisirii liniilor de produs;
- testarea reconcilierii totalului (suma liniilor față de totalul documentului).

**Cine l-ar folosi:**

- echipa OCRInvoice, ca set de test pentru antet, linii și categorisire;
- indirect, utilizatorii aplicației: persoane care își urmăresc cheltuielile (B2C) și contabili care pregătesc documente pentru import (B2B);
- alți studenți sau cercetători care lucrează pe documente contabile.

**Ce există deja:**

| Set                                                                | Dimensiune                                  | Limbă / origine | Ce adnotează                                               | Licență   |
|--------------------------------------------------------------------|---------------------------------------------|-----------------|------------------------------------------------------------|-----------|
| [SROIE (ICDAR 2019)](https://github.com/zzzDavid/ICDAR-2019-SROIE) | 1.000 bonuri scanate (600 train / 400 test) | engleză         | text OCR + 4 câmpuri cheie: companie, adresă, dată, total  | CC BY 4.0 |
| [CORD](https://github.com/clovaai/cord)                            | 1.000 bonuri publice (800 / 100 / 100)      | indoneză        | text + etichete semantice pe linii de produs (30 de clase) | CC BY 4.0 |

**De ce nu ajung:**

- Niciunul nu conține documente românești: lipsesc diacriticele, formatul românesc al datelor și al zecimalelor (12,50).
- SROIE nu adnotează liniile de produs, deci nu poate testa extragerea liniilor sau reconcilierea totalului.

**Ce aduce în plus setul nostru:**

- documente românești, în RON;
- antet, linii și total reconciliat în aceeași înregistrare;
- categorie și subcategorie pe linie, din lista aplicației (alimente, transport, menaj, „Necategorizat”);
- un marcaj de incertitudine pe câmp, ca să putem evalua dacă sistemul semnalează corect ce nu poate citi.

## 2. Surse

- Facturi în engleză: din [SROIE (ICDAR 2019)](https://github.com/zzzDavid/ICDAR-2019-SROIE) – [dataset](https://drive.google.com/drive/folders/1c7LL6GNGLEsLeU8I8gIcyM5o9U_6B0El) (CC BY 4.0, 1000);
- Facturi în engleză: de pe HuggingFace - [dataset](https://huggingface.co/datasets/tanishaiwozo/high-quality-invoice-images-for-ocr) (ODbL, ~8,106);
- Facturi în română: facturi si bonuri proprii.

## 3. Date sensibile

**Conțin datele informații personale?**

Da. Facturile fiscale conțin informații cu caracter personal precum: IBAN-ul, tax id-ul, numele firmelor și adresele complete. Dar în aceasi manieră și cantitățile, prețurile și totalurile, chiar daca nu sunt date personale, sunt informații comerciale confidențiale.

**Dacă da, ce faceți cu ele?**

## 4. Schema

### Invoice

```json
{
  "invoice": {
    "client_name": "string",
    "client_address": "string (may contain newlines)",
    "seller_name": "string",
    "seller_address": "string (may contain newlines)",
    "invoice_number": "string",
    "invoice_date": "string (MM/DD/YYYY format)",
    "due_date": "string (may be empty)"
  },
  "items": [
    {
      "description": "string (may contain newlines)",
      "quantity": "string (numeric format: X.XX)",
      "total_price": "string (numeric format: XX.XX)"
    }
  ],
  "subtotal": {
    "tax": "string (numeric)",
    "discount": "string (may be empty)",
    "total": "string (numeric)"
  }
}
```

### Receipt

```json
{
  "receipt": {
    "seller_name": "string",
    "seller_address": "string (may contain newlines)",
    "seller_tax_id": "string (may be empty)",
    "receipt_number": "string",
    "receipt_date": "string (MM/DD/YYYY format)",
    "receipt_time": "string (HH:MM format)"
  },
  "items": [
    {
      "description": "string (may contain newlines)",
      "quantity": "string (numeric format: X.XX)",
      "total_price": "string (numeric format: XX.XX)",
      "tax_type": "string (e.g., SR, ZRL, may be empty)"
    }
  ],
  "subtotal": {
    "item_count": "string (numeric)",
    "tax": "string (numeric)",
    "discount": "string (may be empty)",
    "total": "string (numeric)"
  },
  "payment": {
    "payment_method": "string (e.g., CASH, CARD)",
    "amount_paid": "string (numeric, may be empty)",
    "change": "string (numeric, may be empty)"
  }
}
```

## Exemple

### Facturi

#### Factura 1: PC Garage, comanda \#11331338

```json
{
  "invoice": {
    "client_name": "Cristina Simion",
    "client_address": "Aleea Dimitrie Anghel , nr 4 , bl M59 , sc A , ap 97\n, et 8 , interf. 197, București (Sectorul 3),\nBucurești, Sector 3",
    "seller_name": "PC Garage S.R.L.",
    "seller_address": "Str. Logofat Tautu 68A, Sector 3, Bucuresti",
    "invoice_number": "PCG07 nr. 8730035",
    "invoice_date": "09/09/2020",
    "due_date": "09/09/2020"
  },
  "items": [
    {
      "description": "Notebook / Laptop ASUS 15.6'' VivoBook S15 M533IA, FHD,\nProcesor AMD Ryzen™ 5 4500U (8M Cache, up to 4.0 GHz),\n8GB DDR4, 512GB SSD, Radeon, No OS, Gaia Green - M533IA-\nBQ042",
      "quantity": "1.00",
      "total_price": "2326.88"
    },
    {
      "description": "Reducere cfm. campanie publicitara - RED_PB",
      "quantity": "-1.00",
      "total_price": "-58.82"
    },
    {
      "description": "Cheltuieli de expeditie - gratuita",
      "quantity": "1.00",
      "total_price": "0.00"
    },
    {
      "description": "Serviciu plata la livrare - ramburs exp",
      "quantity": "1.00",
      "total_price": "10.00"
    }
  ],
  "subtotal": {
    "tax": "432.83",
    "discount": "58.82",
    "total": "2710.89"
  }
}
```

#### Factura 2: PC Garage, comanda \#15617927

```json
{
  "invoice": {
    "client_name": "Cristina Simion",
    "client_address": "Aleea Dimitrie Anghel , nr 4 , bl M59 , sc A , ap 97\n, et 8 , interf. 197, București (Sectorul 3),\nBucurești, Sector 3, BUCURESTI, Sector 3",
    "seller_name": "PC Garage S.R.L.",
    "seller_address": "Str. Gara Herastrau nr. 6, Cladirea Globalworth Square,\net. 2, Sector 2, Bucuresti",
    "invoice_number": "PCG07 nr. 9744932",
    "invoice_date": "03/13/2025",
    "due_date": "03/13/2025"
  },
  "items": [
    {
      "description": "Suport TV / Monitor White Shark GMS-3207 Neferites I, suport\n1 monitor 17-32 inch - NEFERITES-I",
      "quantity": "1.00",
      "total_price": "151.24"
    },
    {
      "description": "Tellur Cablu Organizator 3 culori, 3 buc - FRF000021",
      "quantity": "1.00",
      "total_price": "16.79"
    },
    {
      "description": "Reducere cfm. campanie publicitara - RED_PB",
      "quantity": "-1.00",
      "total_price": "-16.79"
    },
    {
      "description": "Mouse Havit MS78GT Wireless Green - HVMS-MS78GT-GN",
      "quantity": "1.00",
      "total_price": "31.92"
    },
    {
      "description": "Cheltuieli de expeditie - in Bucuresti - FCX",
      "quantity": "1.00",
      "total_price": "6.87"
    },
    {
      "description": "Discount cfm. voucher - DVC",
      "quantity": "-1.00",
      "total_price": "-15.12"
    }
  ],
  "subtotal": {
    "tax": "33.23",
    "discount": "31.91",
    "total": "208.14"
  }
}
```

#### Factura 3: PC Garage, comanda \#16895813

```json
{
  "invoice": {
    "client_name": "Cristina Simion",
    "client_address": "Aleea Dimitrie Anghel , nr 4 , bl M59 , sc A , ap 97\n, et 8 , interf. 197, București (Sectorul 3),\nBucurești, Sector 3, BUCURESTI, Sector 3",
    "seller_name": "PC Garage S.R.L.",
    "seller_address": "Str. Gara Herastrau nr. 6, Cladirea Globalworth Sq, Sector\n2, Bucuresti",
    "invoice_number": "PCG07 nr. 10046370",
    "invoice_date": "10/20/2026",
    "due_date": "10/20/2026"
  },
  "items": [
    {
      "description": "Laptop Lenovo 14'' IdeaPad Slim 5 14IPH11, 2.8K OLED 120Hz,\nProcesor Intel® Core™ Ultra 7 356H (18M Cache, up to 4.70\nGHz), 32GB DDR5, 1TB SSD, Intel Graphics, No OS, Breeze\nMoss - 83S5003PRM",
      "quantity": "1.00",
      "total_price": "5949.58"
    },
    {
      "description": "Lenovo USB-C 65W AC adaptor - GX21W57098",
      "quantity": "1.00",
      "total_price": "104.12"
    },
    {
      "description": "Reducere cfm. campanie publicitara - RED_PB",
      "quantity": "-1.00",
      "total_price": "-104.12"
    },
    {
      "description": "Cheltuieli de expeditie - gratuita",
      "quantity": "1.00",
      "total_price": "0.00"
    },
    {
      "description": "Discount cfm. voucher - DVC",
      "quantity": "-1.00",
      "total_price": "-594.96"
    }
  ],
  "subtotal": {
    "tax": "1124.47",
    "discount": "699.08",
    "total": "6479.09"
  }
}
```

### Bonuri

#### Bon 1: Lidl

```json
{
  "receipt": {
    "seller_name": "LIDL DISCOUNT SRL",
    "seller_address": "MUNICIPIUL BUCURESTI, SECTORUL 2\nSTRADA TONY BULANDRA, Nr. 5 - 13",
    "seller_tax_id": "RO22891860",
    "receipt_number": "00274",
    "receipt_date": "10/26/2026",
    "receipt_time": "13:18"
  },
  "items": [
    {
      "description": "Sacosa maiou",
      "quantity": "1.00",
      "total_price": "0.81",
      "tax_type": "A"
    },
    {
      "description": "Ecotaxa/Cost DEEE",
      "quantity": "1.00",
      "total_price": "0.18",
      "tax_type": "A"
    },
    {
      "description": "Carnati sticks pui",
      "quantity": "1.00",
      "total_price": "8.99",
      "tax_type": "B"
    },
    {
      "description": "Kabanossi sticks porc",
      "quantity": "1.00",
      "total_price": "8.99",
      "tax_type": "B"
    },
    {
      "description": "Drajeuri mix fructe",
      "quantity": "1.00",
      "total_price": "3.99",
      "tax_type": "A"
    },
    {
      "description": "Rulada vanatoreasca",
      "quantity": "1.00",
      "total_price": "7.49",
      "tax_type": "B"
    },
    {
      "description": "30 Oua M sol",
      "quantity": "1.00",
      "total_price": "29.99",
      "tax_type": "B"
    }
  ],
  "subtotal": {
    "item_count": "7",
    "tax": "6.36",
    "discount": "",
    "total": "60.44"
  },
  "payment": {
    "payment_method": "TICHETE DE MASA, CASH",
    "amount_paid": "61.00",
    "change": "0.56"
  }
}
```

#### Bon 2: Farmacia Denis

```json
{
  "receipt": {
    "seller_name": "FARMACIA DENIS S.R.L.",
    "seller_address": "MUNICIPIUL BUCURESTI, SECTORUL 3\nSTR. OCTAVIAN GOGA, NR. 26, BL. V53C, PARTER\nANSAMBLUL CALEA VITAN",
    "seller_tax_id": "RO478111",
    "receipt_number": "389034",
    "receipt_date": "10/26/2026",
    "receipt_time": "17:35"
  },
  "items": [
    {
      "description": "VOLTAREN 140MG EMPLASTRU\nMEDICAMENTOS\n5 BUC DIN 5/CUT",
      "quantity": "1.00",
      "total_price": "46.00",
      "tax_type": "B"
    }
  ],
  "subtotal": {
    "item_count": "1",
    "tax": "4.56",
    "discount": "0.00",
    "total": "46.00"
  },
  "payment": {
    "payment_method": "CARD",
    "amount_paid": "46.00",
    "change": "0.00"
  }
}
```

#### Bon 3: Mega Image

```json
{
  "receipt": {
    "seller_name": "S.C. MEGA IMAGE S.R.L.",
    "seller_address": "BLD. CAMIL RESSU, NR. 7\nSECTOR 3, BUCURESTI",
    "seller_tax_id": "RO6719278",
    "receipt_number": "00074",
    "receipt_date": "10/20/2026",
    "receipt_time": "10:57"
  },
  "items": [
    {
      "description": "RECOMPENSE PISI",
      "quantity": "2.00",
      "total_price": "12.86",
      "tax_type": "B"
    },
    {
      "description": "RISO TART OREZ100G",
      "quantity": "2.00",
      "total_price": "8.16",
      "tax_type": "B"
    },
    {
      "description": "VITA SUC M.C&M 1L",
      "quantity": "1.00",
      "total_price": "11.80",
      "tax_type": "B"
    },
    {
      "description": "GARANTIE AMBALAJ",
      "quantity": "1.00",
      "total_price": "0.50",
      "tax_type": "E"
    }
  ],
  "subtotal": {
    "item_count": "4",
    "tax": "3.00",
    "discount": "2.51",
    "total": "30.81"
  },
  "payment": {
    "payment_method": "CARD",
    "amount_paid": "30.81",
    "change": ""
  }
}
```
