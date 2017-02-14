# Node Island Finder

Finds Islands of Knowledge in Node Git Repositories.

## Useage

`npm install` once.

To analyze a repo: `./analyze /path/to/repo/ [out-dir [tree-ish [subpath...]]]` (the path must be absolute). Optionally pass an target output directory (defaults to `out/`). Optionally pass a [tree-ish](http://stackoverflow.com/a/18605496) (e.g. a commit-sha1) to specify which revision to analyze (defaults to `master`). Optionally pass a whitelist of subpaths in the directory that should be analyzed. For now, if you want to specify a revision or subpaths, you must specify the previous commits (use the defaults if you are unsure).


### Keywords

The `keywords` script takes the file `out/npm_keywords` with contents of the form

```JSON
{"rows": [
  {"key": "modulename", "value": ["list", "of", "tags"]},
  {"key": "modulename", "value": ["list", "of", "tags"]}
]}
```

Additional keys may be given and will be ignored. The script counts the number of occurrences of each tags and prints the result as a JSON object to STDOUT in the form

```JSON
{"tag_a": 5, "tag_b": 4, "tag_c": 2}
```

Tags with only one occurrence are omitted. Tags are converted to lowercase. Tags will be printed in descending order, but purely numeric tags are prepended in random order (and I don't know why).


### modulesToKeywords

Useage: `./modulesToKeywords [in] [out]`.

Takes the CSV report `in` and maps each module to the globally most used keyword from the module's keywords list. Keywords are read from a local npm-registry replicate at `http://localhost:5984/npm-skim/${baseModuleName}`. Which of the given keywords is most used is detemined by looking up each keywords and the number of it's uses in a file `out/keywords_counted.json`, which can be generated by the above `keywords`-script. The results are written to `[out]` and are equal to `[in]`, except they have a row for the keywords prepended. Keywords that have been looked up are written in all-caps. If a module can not be found or if no keywords are given, the module name is returned instead.


### graph_author

Useage: `./graph_author AUTHOR USEAGE_JSON...`

Takes a list of the JSON outputs of the `analyze`-script (2nd parameter) and prints to STDOUT a CSV table that graphs the API useages of the specified **author** (1st parameter). For example (spaces inserted for legibility):

```
> ./graph_author turbopope rev1.json rev2.json rev3.json
         , fs, crypto, path, http
rev1.json, 2 , 0     , 10  , 0
rev2.json, 5 , 0     , 2   , 0
rev3.json, 9 , 1     , 0   , 0
```


### graph_module

Useage: `./graph_module MODULE USEAGE_JSON...`

Like `graph_author`, but for a module instead of an author.

Takes a list of the JSON outputs of the `analyze`-script (2nd parameter) and prints to STDOUT a CSV table that graphs the API useages of the specified **module** (1st parameter). For example (spaces inserted for legibility):

```
> ./graph_author crypto rev1.json rev2.json rev3.json
         , john, jade, rose, dave
rev1.json, 3   , 0   , 11  , 0
rev2.json, 4   , 0   , 3   , 0
rev3.json, 8   , 2   , 0   , 0
```


### analyze_history

Useage: `./analyze_request_history /path/to/repo/ [out-dir [n [subpath...]]]`

Analyze every `n`th revision of the given repo with `analyze` (defaults to `n=250`). `out-dir` and `subpath...` are the same arguments as for `analyze` and are passed through to it.
