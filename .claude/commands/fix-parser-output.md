---
description: Fix parser output
---

In order to get the json representation of parsed result use `-dump-ast` flag. For a given file `./corpus/bal/${path}/foo.bal` you can find the expected json representation in `./corpus/parser/${path}/foo.json`. When fixing parser output errors first check if the difference is due to differences in encoding/escape characters. If that is the case simply update the expected file to match the new output. Otherwise you should figure out what is the difference in the parsing logic between `parser.go` and `BallerinaParser.java`. When fixing you must exactly fallow the same logic as the java code and fallowing the migration guidelines in `CLAUDE.md`
