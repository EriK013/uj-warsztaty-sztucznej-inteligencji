# Tłumaczenia transkrypcji TED

Rozszerzenie datasetu MAPLE. Wybieramy prelekcje TED, które mają napisy w kilku językach naraz, i zlecamy modelom językowym przetłumaczenie angielskich napisów na pozostałe języki. Oficjalne tłumaczenie TED zostaje jako punkt odniesienia, więc można porównać tłumaczenie modelu z tłumaczeniem człowieka. Pracujemy na formacie SRT, więc czasy napisów są zachowane.

Języki: z angielskiego na `zh`, `fi`, `fr`, `he`, `ja`, `pl`.

## Jak to działa

Pięć notebooków, jeden po drugim. Każdy zapisuje plik, który czyta następny.

1. `ted_transcripts_finder.ipynb`. Przez API GraphQL TED szuka prelekcji, które mają napisy we wszystkich naszych językach i mieszczą się w zadanych latach. Pomija TEDx-y. Wynik: `ted_candidates.csv`.
2. `ted_transcripts_extractor.ipynb`. Dla znalezionych slugów pobiera tytuł, opis i transkrypcje (czysty tekst oraz SRT). Wynik: `ted_talks_transcripts.tsv`.
3. `LLMs/prepare_prompts.ipynb`. Wstawia transkrypcje do szablonu `prompt_template.txt`. Powstaje jeden prompt na prelekcję i język. Wynik: `prompts.tsv`.
4. `LLMs/plgrid_client_v_1.ipynb`. Wysyła prompty na PLGrid i zbiera odpowiedzi. Asynchronicznie, z ponawianiem przy błędach i wznawianiem po przerwaniu. Wynik: `responses/<model>/responses.tsv`.
5. `LLMs/results_processing.ipynb`. Czyści odpowiedzi (wycina reasoning, prostuje SRT) i scala je po slugu. Na końcu powstaje jeden CSV na język w `data/dataset/`.

## Uruchomienie

Zależności instalują się w pierwszych komórkach każdego notebooka.

Klient PLGrid szuka klucza w `LLMs/api_key.txt` (plik trzymamy poza repo):

```
plgrid=tutaj_klucz
```

**Notebooki uruchamiamy po kolei od 1 do 4**
