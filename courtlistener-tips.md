> Useful tips for performing queries on CourtListener  
> source: https://www.courtlistener.com/help/search-operators/
---
**OPINIONS**
| Field | Description |
|-------|-------------|
| id | The ID of the item in the CourtListener system. |
| docket_id | The ID of the docket associated with the item. |
| scdb_id | The ID of an opinion in the Supreme Court Database. |
| cluster_id | ID of the opinion cluster. A single case can include dissents, concurrences, etc., grouped as a “cluster.” |
| sibling_ids | IDs of other opinions within the same cluster. |
| court_id | Abbreviated court ID. See jurisdictions list for valid abbreviations. |
| attorney | Attorneys that argued the case. |
| author_id | ID of the judge who authored the opinion. |
| panel_ids | IDs of judges on the panel for the case. |
| panel_names | Full names of judges on the panel. |
| joined_by_ids | IDs of judges who joined the opinion author. |
| judge | Full-text searchable judge name (useful if no ID exists). |
| per_curiam | Indicates whether the opinion was issued per curiam. |
| dateFiled | Date the decision was issued. |
| dateArgued | Date the case was first argued. |
| dateReargued | Date the case was reargued. |
| dateReargumentDenied | Date a motion for reargument was denied. |
| caseName | Name of the case. |
| docketNumber | Docket number of the case. |
| citation | All citations associated with the opinion. |
| neutralCite | Neutral citation for the opinion. |
| lexisCite | LexisNexis citation. |
| suitNature | Nature of the suit. |
| citeCount | Number of times the opinion has been cited (supports range queries). |
| status | Precedential status: published, unpublished, errata, separate, in-chambers, relating-to, unknown. |
| cites | IDs of items that cite this opinion. |
| type | Opinion type: combined-opinion, unanimous-opinion, lead-opinion, plurality-opinion, concurrence-opinion, in-part-opinion, dissent, addendum, remittitur, rehearing, on-the-merits, on-motion-to-strike. |
| procedural_history | History of the case as it moved between courts. |
| posture | Procedural posture of the case. |
| syllabus | Summary of issues presented and the outcome. |
| non_participating_judge_ids | Judges who heard the case but did not participate in the opinion. |

## CourtListener `source` Codes

*Where does your data come from?  
Use these codes to maintain referential integrity.*

| Code | Origin |
|------|--------|
| C | Court website |
| R | public.resource.org |
| CR | Court website merged with public.resource.org |
| L | Lawbox |
| LC | Lawbox merged with court website |
| LR | Lawbox merged with public.resource.org |
| LCR | Lawbox merged with court website and public.resource.org |
| M | Manual input |
| A | Internet Archive |
| H | Brad Heath archive |
| Z | Columbia archive |
| U | Harvard Case Law Access Project |
| CU | Court website merged with Harvard |
| D | Direct court input |
| Q | 2020 anonymous database |
| QU | 2020 anonymous database merged with Harvard |
| CRU | Court website merged with public.resource.org and Harvard |
| DU | Direct court input merged with Harvard |
| LU | Lawbox merged with Harvard |
| LCU | Lawbox merged with court website and Harvard |
| LRU | Lawbox merged with public.resource.org and Harvard |
| LCRU | Lawbox merged with court website, public.resource.org, and Harvard |
| MU | Manual input merged with Harvard |
| RU | public.resource.org merged with Harvard |
| ZA | Columbia merged with Internet Archive |
| ZD | Columbia merged with direct court input |
| ZC | Columbia merged with court website |
| ZH | Columbia merged with Brad Heath archive |
| ZLC | Columbia merged with lawbox and court website |
| ZLR | Columbia merged with lawbox and public.resource.org |
| ZLCR | Columbia merged with lawbox, court website, and public.resource.org |
| ZR | Columbia merged with public.resource.org |
| ZCR | Columbia merged with court website and public.resource.org |
| ZL | Columbia merged with lawbox |
| ZM | Columbia merged with manual input |
| ZQ | Columbia merged with 2020 anonymous database |
| ZU | Columbia archive merged with Harvard |
| ZLU | Columbia archive merged with lawbox and Harvard |
| ZDU | Columbia archive merged with direct court input and Harvard |
| ZLRU | Columbia archive merged with lawbox, public.resource.org, and Harvard |
| ZLCRU | Columbia archive merged with lawbox, court website, public.resource.org, and Harvard |
| ZCU | Columbia archive merged with court website and Harvard |
| ZMU | Columbia archive merged with manual input and Harvard |
| ZRU | Columbia archive merged with public.resource.org and Harvard |
| ZLCU | Columbia archive merged with lawbox, court website, and Harvard |

