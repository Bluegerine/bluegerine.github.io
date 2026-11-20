# Research Toolkit

Two Python projects for tech content research.

## 1. claimlog (Python)

A command-line source tracker. It stores each claim with its source, date, verification status and next check, then exports to CSV or Markdown.

```bash
python claimlog.py add "Nvidia named an interim CEO" "https://example.com/story" --status multiple_sources
python claimlog.py set 1 confirmed_primary --next-check "Matched to SEC 8-K"
python claimlog.py list --status unchecked
python claimlog.py export --format md
```

Built with the standard library only: `argparse`, `sqlite3`, `csv`, `dataclasses`. Input is validated, and unit tests are included.

## 2. Research Radar (AI automation)

A pipeline that monitors tech news feeds, groups coverage of the same story across outlets, extracts the factual claims with Claude, logs them in claimlog, and writes a Markdown digest to verify.

```bash
python research_radar.py --topics nvidia,chip            # no AI, fallback extraction
export ANTHROPIC_API_KEY=your-key
pip install anthropic
python research_radar.py --topics nvidia,chip --llm      # AI claim extraction
```

How it works:
1. Fetches RSS and Atom feeds (a broken feed is skipped, not fatal).
2. Filters by topic keywords and skips stories seen in earlier runs.
3. Groups headlines about the same story using word-overlap similarity.
4. Extracts up to 5 claims per story with Claude, falling back to a simple extractor if the API call fails.
5. Labels each claim `single_source` or `multiple_sources`. It never marks anything `confirmed_primary`: a human checks the primary source.
6. Writes `digests/digest-YYYY-MM-DD.md`.

Run the tests: `python -m unittest -v`

## Ideas to extend (to grow the project)
- Run daily with cron or GitHub Actions and email the digest.
- Add an "ask Claude to find the primary source" step for each claim.
- Add a small web page that shows the tracker.
- Add more feeds, including Africa-focused outlets like TechCabal.
