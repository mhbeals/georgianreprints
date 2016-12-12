# Scissors and Paste: The Georgian Reprints
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.200399.svg)](https://doi.org/10.5281/zenodo.200399)

## Contents
+ Description
+ Background
+ Methods and caveats
  + Essential background reading
  + The source data
  + Data processing
  + Caveats
+ Data summary
  + Notes
  + Tables
+ Citation
+ Acknowledgments
+ License
+ Future plans

## Description

This dataset describes instances of reprinting and text reuse (scissors-and-paste journalism) in British newspapers between 1800-1837. It was derived from the 19th-Century British Library Newspapers, Part 1 digitised newspaper collection by using plagiarism detection software to identify instances of substantially similar text. It contains a series of manifests that describe a) instances of shared content b) the likely directionality of copying and c) which instances are evolutionary dead-ends and have no known reprints. It is part of the [Scissors and Paste Project](https://osf.io/nm2rq/)

## Background

Before 1837, electronic telegraphy was in its infancy and was not employed in the transmission of news content. Instead, newspapers within and beyond Britain engaged in scissors-and-paste journalism, wherein one newspaper copied, in part or in whole, material from other publications. This created a highly decentralized, global news network. Although the level of reprinting varied between titles, the practice was largely seen as mutually beneficial and in the wider public interest. To that end, both the British and United States governments subsidised postal exchanges between newspapers and larger papers employed exchange editors to curate incoming material for republication.

Scissors-and-paste practices can be seen in all types of periodical material, including news, correspondence, literature, poetry, jokes and advertisements. The degree to which attribution was professionally expected is a matter of ongoing research, but even when attribution did take place, it was given unsystematically; sometimes newspapers listed the date and title of the original publication, rather than the one from which they had directly copied, while other times they offered only basic clues, such as ‘a London paper’.  This has led to a sense of frustration, and several honest mistakes, by those using newspapers as indicators of local or regional public opinion; this lack of clear attribution, alongside anonymous or pseudonymous authorship, leaves the modern reader unsure as to the true origin of a given text. Matching texts within 19th-century corpora computationally allows us to work with reprinted and reworked materials with a greater confidence as to their provenance. News content, broadly defined as the time-sensitive recordings of events, was likely to be reprinted quickly and maintain a high fidelity regardless of the number of generations, making it particularly well suited for electronic discovery. 

[The Scissors and Paste Project](http://www.scissorsandpaste.net) tracks reprinting and reuse in the long-19th century (1783-1914) across the Anglophone world. The initial phase of the project involved the development of a suite of tools and methodologies to efficiently identify reprint families and then suggest both directionality and branching within these subsets. From these case-studies, detailed analyses of additions, omissions and wholesale changes can offer insights into the mechanics of reprinting that left behind few if any other traces in the historical record. 

The Georgian Reprints represents the first discrete dataset to come out of this project, focusing on the years 1800-1837 within Great Britain. It is comprised of 1,824 monthly listings of reprinting within the 19th-Century British Library Newspaper collection. As the wider project progresses, it is expected that further datasets for additional years and wider corpora will be made available through the [project website](https://osf.io/nm2rq).

## Methods and caveats

### Essential background reading:

Beals, M. H. “Stuck in the Middle: Developing Research Workflows for a Multi-Scale Text Analysis” *Journal of Victorian Culture*, 21 no. 4 (2017)

### The source data

Two-hundred twenty-six thousand, five-hundred seven (226507) page-level XML transcriptions files from the years 1800-1837 of the 19th-Century British Library Newspapers, Part 1 collection. The original collection contained text from 51 newspaper titles, regularised into 31 distinct publications. The original XML dataset contained 42 corrupted files that could not be transformed into plain text transcriptions; a full listing is available on the [dynamic repository]( https://github.com/mhbeals/BL19thC_Reprints/tree/master/Errors). All other pages within the collection were analysed.

Transcriptions within the original collection were obtained through optical character recognition (OCR) from digitised images of individual pages. The accuracy rate for the machine-readable transcriptions varies significantly from publication to publication and page to page. While these errors are unlikely to result in false positives (see data processing below), they may have caused a large number of false negatives. Therefore, the number of true matches is almost certainly higher than those recognised by the computer-aided comparison process described below; these manifests should, therefore, be considered to represent a minimum rather than an average or maximum reprinting rate for any given title or period.

Documentation as to the editions or individual copies digitised by the original British Library project were not indicated in the page-level metadata and could not be accounted for in the text comparison process.

The transcriptions used in the text comparison process were at page rather than article resolution; that is, whole pages of text rather than a smaller subdivision of it. This decision was taken owing to (a) the imprecision of computational subdivision for this period and (b) the improvement in the matching of ancestors and descendants when there was evidence of multiple reprints from a single source.

### Data processing

#### Normalisation of periodical names

Over the thirty-seven (37) year period, several publications altered their official title. During the initial comparison process, the title indicated by the **title** tag in the original XML collection was used. In the final derived dataset, these titles have been normalised to enable consistent analysis across all years. A full manifest of titles and their normalisations in the derived dataset has been included. Versions of this data without this normalisation can be found at the [dynamic Scissors and Paste github repository](https://osf.io/nm2rq).

#### Plaintext transcription files

The original XML transcriptions files were transformed using XSL (included in the dataset) into non-encoded plaintext files; that is, all metadata and XML tags were removed. The following metadata tags were retained and used to name the plaintext files:

+ NormalisedDate
+ title
+ pageSequence

#### Matching with Copyfind64 v.4.1.4

The plaintext files were first analysed, and instances of shared text found, using [Copyfind64 v4.1.4 by Lou Bloomfield](http://plagiarism.bloomfieldmedia.com/wordpress/software/copyfind/). The following settings were used.

+ PhraseLength = 10
+ WordThreshold = 200
+ SkipLength = 20
+ MismatchTolerance = 5
+ MismatchPercentage = 50
+ BriefReport = False
+ IgnoreCase = True
+ IgnoreNumbers = True
+ IgnoreOuterPunctuation = True
+ IgnorePunctuation = True
+ SkipLongWords = False
+ SkipNonwords = False

These setting were designed to be as forgiving as possible to OCR errors while not accruing an unmanageable number of false positives. The collection was divided into one-month (1) sets across the thirty-seven (37) year period. Each of these monthly sets was compared against itself and the succeeding seven (7) months. Manual testing suggested that any matches after 200 days were either false positives, annual notices, advertisements or miscellany content rather than news. The manifests outputted by Copyfind64—the raw matching reports—were then further processed by a series of heuristic filters.

#### Accounting for False Positives

The raw matching reports were processed by two sets of heuristics, [Memetracker v1.0.0 (doi: 10.5281/zenodo.198542)](https://github.com/mhbeals/memetracker) and [ReprintMapper  v1.0.0  (doi: 10.5281/zenodo.198564)](https://github.com/mhbeals/reprintmapper).

Memetracker applied three (3) basic filtering heuristics to remove false positives from the raw matching reports. The first removed all self-matches; that is, reprints within a single title. Manual testing indicated consistently that these were advertisements, notices or other forms of boilerplate text. The second heuristic removed all matches that exceeded 200 days.  This harmonized the data, as the initial seven-month (7) filter within Copyfind64 varied slightly throughout the year. The final heuristic more precisely constricted the word count required for a match. Texts had to have either an overall match of 160 words, or a left or right match of 90 words. These levels were chosen by testing a random sample of possible matches from the year 1815 to remove as many false positives as possible while retaining all true matches.

ReprintMapper, unlike Memetracker, describes specific ancestor-descendent relationships rather than all matching content. It applies identical heuristics as Memetracker before applying additional processing instructions. First, it removed all same-day matches. Although it was technically possible for one newspaper to reprint material from another on the same day, the lack of edition metadata and the paucity of newspapers printed in the same geographical location made such matches highly unlikely. Future iterations of this dataset may take geographical information into account more precisely.

Next, it compared all possible predecessors of each reprint on the number of matching words and the date difference. The match with the highest fidelity was determined to be the most likely ancestor of that reprint. Comparing computer and manually created stemma (trees) indicated that ordering on raw word matches resulted in identical or near-identical results to ordering based on close reading. Where two matches had identical fidelity, the earlier match was determined to be the ancestor as, in the absence of other information, it was logical to ascribe ancestry to the earliest possible source. 

A manifest of these ancestor-descendent relationships was then outputted. A second manifest was also created of all pages that appeared to be evolutionary dead-ends; that is, where they did not appear to be the ancestor of any subsequent reprints.

### Caveats

Although a complete representation of the original digitised newspaper corpus, there are some key limitations to the data within the Raw Matching Reports. First, the 19th-Century British Library Newspapers, Part 1 collection contains only 31 titles and does not represent a complete corpus of the British press for this period. Careful examination of which titles are included is recommended. Second, the Raw Matching Reports only list those document pairs for which 200 words of shared content can be computationally identified. As the machine-readable transcriptions of these pages were obtained through optical character recognition (OCR) from digitised images, the accuracy rate can vary significantly; some pages are largely illegible. While these errors are unlikely to result in false positives, they may have caused a large number of false negatives. Therefore, the number of true matches is certainly higher than those recognised by the comparison process; these manifests should, therefore, be considered a minimum rather than an average or maximum reprinting rate for any given title or period. Likewise, ReprintMapper can only find the best match within the corpus. If two descendants of a single ancestor are present, but their common ancestor is not, ReprintMapper will link the later to the earlier version, even if these actually represent two different branches. This false positive must be excluded manually by using contextual knowledge. Finally, documentation as to the editions or individual copies digitised by the original British Library project were not indicated in the page-level metadata and could not be accounted for in the text comparison process. 

An important final proviso is that the transcriptions used in the text comparison process were at page rather than article resolution; that is, whole pages of text rather than a smaller subdivision of it. This decision was taken owing to (a) the imprecision of computational subdivision for newspapers from this period and (b) the improvement in the matching of ancestors and descendants when there was evidence of multiple reprints from a single source. However, if a page has reprints from two separate ancestors within the corpus, ReprintMapper will only link it to source with the larger match; the other connection will be lost. Iterations of process at article level would produce additional pairs but lose the added certainty obtained from matching multiple-article reprints.

## Data

### General notes

The current version is 1.0, released December 2016.

The dataset contains one-thousand eight-hundred twenty-four (1824) TSV files, divided into four (4) directories.  Each directory contains four-hundred fifty-six (456) files, each representing one month between January 1800 and December 1837. The data tables are supplied in TSV (tab separated values) format, which can be readily imported into spreadsheets and database software.

### Tables

#### RawMatchingReports

Files in this directory represent all matches as determined by Copyfind64, the month of the filename referring to that of the earlier of the two pages.

The columns of data are as follows:

| Field   | Description |
|---------|--------------------------------------------------------------------------------------------------------------------------------|
|PM       |The number of perfectly matching words in phrases of at least 10 words                                                          |
|OL       |The number of perfectly and imperfectly matching words in phrases of at least 10 words in the later (Reprint) document		   |
|OR       |The number of perfectly and imperfectly matching words in phrases of at least 10 words in the earlier (Original) document	   |
|RYEAR    |This column indicates the year in which the later of two matching pages was printed.											   |
|RMONTH   |This column indicates the month in which the later of two matching pages was printed.										   |
|RDAY     |This column indicates the day in which the later of two matching pages was printed.											   |
|RTITLE   |This column indicates the title of the later of two matching pages.															   |
|RFILENAME|This column indicates the filename of the later of the two matching pages. Filenames are formatted as (yyyy.MM.dd_Title_page).  |
|OYEAR    |This column indicates the year in which the earlier of two matching pages was printed.										   |
|OMONTH   |This column indicates the month in which the earlier of two matching pages was printed.										   |
|ODAY     |This column indicates the day in which the earlier of two matching pages was printed.										   |
|OTITLE   |This column indicates the title of the earlier of two matching pages.														   |
|OFILENAME|This column indicates the filename of the earlier of the two matching pages. Filenames are formatted as (yyyy.MM.dd_Title_page).|

#### Memes

Files in this directory list those pairs of pages that share a significant amount of content. Individual matches are only listed once; that is, B-A and not also A-B. 

The columns of data are as follows:

| Field    |  Description                                                                                                                   																																																			|
|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RYEAR    | This column indicates the year in which the later of two matching pages was printed.                                                                                                                                                                                                                                                       |
| RMONTH   | This column indicates the month in which the later of two matching pages was printed.                                                                                                                                                                                                                                                      |
| RDAY     | This column indicates the day in which the later of two matching pages was printed.                                                                                                                                                                                                                                                        |
| RTITLE   | This column indicates the title of the later of two matching pages.                                                                                                                                                                                                                                                                        |
| RPAGE    | This column indicates the page number of the later of two matching pages. Page numbers were given an S prefix w when two editions of the same date-title combination were discovered within in the original XML collection. In the original collection, these may have been designated with either an S or a V in the pageSequence value.  |
| OYEAR    | This column indicates the year in which the earlier of two matching pages was printed.                                                                                                                                                                                                                                                     |
| OMONTH   | This column indicates the month in which the earlier of two matching pages was printed.                                                                                                                                                                                                                                                    |
| ODAY     | This column indicates the day in which the earlier of two matching pages was printed.                                                                                                                                                                                                                                                      |
| OTITLE   | This column indicates the title of the earlier of two matching pages.                                                                                                                                                                                                                                                                      |
| OPAGE    | This column indicates the page number of the earlier of two matching pages. Page numbers were given an S prefix w when two editions of the same date-title combination were discovered within in the original XML collection. In the original collection, these may have been designated with either an S or a V in the pageSequence value.|

#### AncestorDescendent

Files in this directory list every page within the corpus, linking it to the one match that is most likely its direct ancestor (though not necessarily its direct predecessor). If there are no earlier variants of the page, the page is excluded from the list. The column headers are identical to those in Memes.

#### Deadends

Files in this directory describe pages that do not have any descendent pages, as determined by ReprintMapper.

The columns of data are as follows:.

| Field   |  Description                                                                                                                                                                                                                                                                                           |
|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| YEAR    | This column indicates the year in which the page was printed.                                                                                                                                                                                                                                          |
| MONTH   | This column indicates the month in which the page was printed.                                                                                                                                                                                                                                         |
| DAY     | This column indicates the day in which the article page printed.                                                                                                                                                                                                                                       |
| TITLE   | This column indicates the title in which the article page printed.                                                                                                                                                                                                                                     |
| PAGE    | This column indicates the page number. Page numbers were given an S prefix w when two editions of the same date-title combination were discovered within in the original XML collection. In the original collection, these may have been designated with either an S or a V in the pageSequence value. |

## Citation

M. H. Beals. (2016). Scissors and Paste: The Georgian Reprints v1.0.0 [Data set]. Zenodo. http://doi.org/10.5281/zenodo.200399

## Acknowledgments

The derived dataset was created using the XML transcriptions of the 19th-Century British Library Newspapers, Part 1 collection, maintained by the British Library. I am grateful to James Baker and Mahendra Mahey of British Library Labs for their support of the project and their work in securing the necessary permissions for use of the XML collection.  I would also like to express my thanks Sharon Howard for use of her data documentation template, and Gareth Cole, Adam Crymble and Sharon Howard for their insights on an earlier version of this documentation.

The original documents are held by the British Library.

## License

The dataset and all accompanying documentation are licensed under a Creative [Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

This means that you are free to copy and redistribute the material in any medium or format; and to remix, transform, and build upon the material for any purpose, even commercially, providing you give appropriate credit, provide a link to the license, and indicate if changes were made.

## Future plans

+ Inclusion of other 1800-1837 newspaper transcription datasets
  + British Library Newspapers, Part 2
  + Trove
  + Chronicling America
+ Refining ReprintMapper connections with geographical data
+ Direct links to facsimile versions of newspaper pages
