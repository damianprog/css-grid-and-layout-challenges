# CSS Grid — plan challenge'ów

Nov 2, 2026 · @Damian

## O kursie i zasady

10 challenge'ów do kursu [Ultimate CSS Grid & Layout Techniques, v3](https://master.dev/courses/css-grid/) (Jen Kramer, \~6h20m). Każdy wpada po bloku lekcji i celowo różni się od ćwiczeń z kursu.

- Czysty CSS, bez Tailwinda i bez wspólnego motywu — każdy challenge to osobny CodePen lub plik.
- Po skończeniu bloku piszesz w projekcie, którą lekcję skończyłeś; dostajesz rozpisany challenge (z HTML-em startowym, jeśli trzeba).
- Rozwiązujesz sam, w trybie zagadki: pytania i podpowiedzi zamiast gotowego CSS.

## Challenge'e

| #   | Po lekcjach                                             | Challenge                                | Wymagania i ograniczenia                                                                                                                                                                                   | Status      |
| --- | ------------------------------------------------------- | ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| C1  | Grid Overview → Basic Grid Exercise                     | Profil użytkownika                       | Avatar po lewej, imię i bio po prawej, wyśrodkowane w pionie; pod spodem 3 statystyki w równych kolumnach. Tylko grid i gap, bez flexboxa i marginesów do pozycjonowania. Sprawdź w DevTools grid overlay. | Nie zaczęty |
| C2  | Grid Layout Exercise → Grid Span & Repeat Exercise      | Bento grid                               | Siatka 4×3, 6 kafelków: jeden 2×2, dwa 2×1, reszta 1×1. Najpierw numerami linii, potem refaktor na span i repeat().                                                                                        | Nie zaczęty |
| C3  | Grid Template Area (+ ćwiczenie)                        | Layout strony                            | Header, sidebar, main, aside, footer wyłącznie przez grid-template-areas. Bonus: sidebar na prawo przez zmianę samego stringa obszarów.                                                                    | Nie zaczęty |
| C4  | Cała sekcja Responsive Design & Media Queries           | Responsywny dashboard, mobile-first      | 1 kolumna → od 600px 2 kolumny z wyróżnionym kafelkiem na całą szerokość → od 1000px 4 kolumny i inna kolejność obszarów. Kolejność w HTML bez zmian.                                                      | Nie zaczęty |
| C5  | Overlapping Rows & Columns → CSS Animated Grid Exercise | Hero banner                              | Tło, gradient i tekst nałożone przez grid, bez position: absolute. Bonus: karta z opisem wjeżdżającym na obrazek na :hover.                                                                                | Nie zaczęty |
| C6  | Grid Margin → minmax, auto-fit & auto-fill              | Galeria bez media queries                | Kafelki min. 180px, liczba kolumn dopasowuje się sama. Wersje auto-fit i auto-fill, przetestuj na 2 i 10 elementach; różnica jednym zdaniem.                                                               | Nie zaczęty |
| C7  | Subgrid → Subgrid in Action                             | Cennik z 3 planami                       | Różne długości treści; nagłówki, ceny, listy i przyciski w jednej linii. Najpierw bez subgridu, potem z subgridem.                                                                                         | Nie zaczęty |
| C8  | Moons of Saturn (cała sekcja)                           | Strona listy artykułów — egzamin z gridu | 3 breakpointy, featured post, subgrid w kartach, overlap w hero. Breakpointy planujesz sam, bez instrukcji krok po kroku.                                                                                  | Nie zaczęty |
| C9  | Sekcje Flexbox + Responsive Images                      | Navbar + sekcja produktu                 | Navbar we flexie: logo w środku na desktopie (order), zawijanie na mobile. Zdjęcie przez picture (inny kadr na mobile) oraz srcset/sizes. Uzasadnij: gdzie flex, gdzie grid.                               | Nie zaczęty |
| C10 | Sekcja Container Queries (final)                        | Karta w 3 kontekstach                    | Ta sama karta w sidebarze, siatce 2-kolumnowej i na pełnej szerokości; układ przez @container, bez zmiany klas. Bonus: przełącznik motywu przez style query.                                               | Nie zaczęty |
