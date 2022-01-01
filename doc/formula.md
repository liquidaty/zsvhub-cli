## Column references

### "Raw" data columns

### Dictionary columns

### View columns

## Functions

A number of functions can be used in formulaic expressions:

|Name|Description|Returns|Example|
|--|--|--|--|
|abs|absolute value|absolute value|abs(-5) return 5|
|alphacount|counts the number of characters in…|number of characters in given stri…|alphacount("abc 09x321 d!") return…|
|numcount|counts the number of characters in…|number of characters in given stri…|numcount("abc 09x321 d!") returns 5|
|arange|array range: create an array of nu…|array of results, or null if an ar…|arange(10, 25, 5) returns {10, 15,…|
|afilter|array filter: filters arg1 based o…|array of results for which arg2 ev…|afilter(array(1,2,3), 0, [] < 3) r…|
|aconcat|concatenates two arrays|array of results|aconcat(array(1,2), array(3,4)) re…|
|allornone|check if all args are null, or all…|(null, null) returns true; allorno…|allornonevalue1|
|amap|array map: evaluates arg2 (and any…|array of results. If arg1 is not a…|amap(array(1,2,3), [] < 3) returns…|
|areverse|reverse the order of an array|array of results. If arg1 is not a…|areverse({1,2,3} returns a {3,2,1}|
|amost_frequent|retrieve the most frequent item fr…|array element|amost_frequent{a,b,c,a,a,e,d,b,a,c…|
|apush|append values to array (or null to…|array of results|apush(array(1,2), 3,4) returns {1,…|
|arrayrotate|convert array of R elements, each …|array of results. If arg1 is not a…|arrayrotate((1,2),(a,b),(7,8)) ret…|
|array|creates an array from the given ar…|array|array(a,b,c) returns array of (a, …|
|arraycol||||
|arrayconjoin||||
|arraycontains|value_to_find|position + 1 of the item in the ar…|arraycontains(#myarray, "abc") whe…|
|arraydistinct|filter distinct values. if 2nd arg…|array of distinct values||
|arrayjoin||||
|!arrayjoinnested||||
|arrayjointext|joins arrays and/or scalars into a…|string of joined text|arrayjointext("a", "b", "c", "d", …|
|arraymset|sets the value of an element of a …|null|arraymset(#myarray, "two", 2, 3) s…|
|arrayset|sets the value of an array element|null|arrayset(#myarray, 2, "two") sets …|
|arraysize|get size of an array|size of array||
|arraysort|sort an array of values|sorted array|arraysort(array(5,3,9)) returns an…|
|arrayval|retrieve a value from within an ar…|array element|arrayval(my_array, 1) returns the …|
|arrayvals|retrieve multiple values from cont…|array subset|arrayvals(array(1,2,3,4,5), 2,4) r…|
|afindindex|array find index: finds index of f…|0-based index of first element mee…|afindindex(array(1,2,3), [] > 1) r…|
|between|true/false if value is between two…|true/false|between(x, 3, 7) returns true if x…|
|bestmap|(experimental) finds the map that …|name of highest-match map. If two …|bestmap(array("F","L","LTD","SISA"…|
|bucketrange|bucketrange: return {x,y,z} where …|{x,y,z} or null if an arg is inval…|bucketrange(0, 582, 741, 10, 5, 5)…|
|capitalize|capitalize a word|capitalized value||
|cached_eval|evaluates a formula and caches the…|result of evaluated formula|cached_eval("i",1+2) returns 3, an…|
|charsout|counts the number of characters (c…|number of characters in given stri…|charsout("foobar1","abcdef") retur…|
|charsin|counts the number of characters (c…|number of characters in given stri…|charsin("foobar1","abcdef") return…|
|cmp|Compare two values. Default compar…|||
|coalesce|find non-null value|first non-null argument|coalesce(null, null, 10, "abc") re…|
|column||||
|columnval||||
|consolidate||||
|csv|convert to csv value|csv value (surrounds with double-q…|csv("abc,de\\"f") returns "\\"abc,…|
|tsv|convert to tsv value|tsv value|tsv("abc\\tdef") returns "abc def"|
|datacheck_result|return results of given datacheck …|datacheck list name|datacheck_result"my_datacheck_list…|
|date|date|date value|date(2014,3,1) returns date value …|
|dateadd|calculate a date relative to the g…|||
|datediff|calculate the time period between …||datediff('m', [Cutoff], [Maturity]…|
|days360|calculate the number of days betwe…|||
|datatype|datatype of the given expression|'N', 'B', 'D' or 'S' for number, b…|datatype([My Balance Col]) returns…|
|datemonthend|calculate the last day of the give…|date representing the last day of …|datemonthend(date(2013,3,21)) retu…|
|datemonthstart|calculate the first day of the giv…|date representing day 1 of the mon…|datemonthstart(date(2013,3,21)) re…|
|datepart|returns the value of a part of a d…|||
|dblookup|server-side database lookup: execu…|value if only one column queried, …|dblookup("mydb", "column1", "abc =…|
|dblookup_volatile|server-side database lookup: execu…|value if only one column queried, …|dblookup_volatile("mydb", "column1…|
|dblookup_dict|server-side database lookup: execu…|dictionary of result values|dblookup_dict("mydb", "column1", "…|
|dblookup_dict_volatile|server-side database lookup: execu…|dictionary of result values|dblookup_dict_volatile("mydb", "co…|
|dslookup|data source lookup: look up a spec…|value of the specified column refe…|dslookup("[Current LTV]") returns …|
|!debug|for debugging|||
|decimals|return number of decimal places to…|number >= 0||
|dict_add|sets a key-value pair in a diction…||dict_add(#mydict, "NY", "New York"…|
|dict_delete|removes a key-value pair in a dict…||dict_delete(#mydict, "NY", "New Yo…|
|dict_contains|return true/false if specified key…||dict_contains(#mydict, "NY") retur…|
|dict_get|return value (or first of array of…||dict_get(#mydict, "NY") returns "N…|
|dict_getall|return value (or full array of val…||dict_getall(#mydict, "list_of_some…|
|dict_get_or_set|return value (or first of array of…||dict_get_or_set(#mydict, "NY", "Ne…|
|dict_keys|return array of keys from given va…|||
|dict_closest_score|return score of closest value in d…|score corresponding to closest val…||
|dict_closest_val|return key of closest value in dic…|key corresponding to closest value||
|lookup_exact|return key of closest value in dic…|key corresponding to closest value||
|lookup_fuzzy|return key of closest value in dic…|key corresponding to closest value||
|dict_walk|execute something for each value o…|||
|dy|(experimental) dynamic function th…|||
|env|returns the value of an environmen…|||
|eval|evaluates the first argument as a …|result of evaluation|eval("1 + 1") return 2)|
|exp|exp math function|||
|fetch|fetch from url|url response as dictionary|fetch(env("persistent_url","is") +…|
|filename|return filename portion of string|filename component, excuding exten…|filename('/path/to/abc.txt') retur…|
|flatten|flatten a nested array|||
|floor|floor of floating-point value|||
|format|format a number or date|||
|formula2str|convert formula to string form|stringified formula|formula2str([abc], 1) returns '*.[…|
|globaltableval|(experimental) look up a value wit…|||
|get_named_tags|return list of current input tags …|list of tags|get_named_tags('year') returns ['2…|
|has_named_tag|check if the current input has the…|true/false|has_named_tag('year', '2018') retu…|
|htmlesc|html-escape a string|||
|if|if-then-[if-then...][-else]||if(1, "do this", "else do this") w…|
|ifthen|returns result of evaluating the g…||ifthen("myIfThenTable") will evalu…|
|invert_transform|apply transformation inversions of…|formula result, after application …||
|isarray|check if a value is an array|true/false||
|isbool|check if a value is or can be conv…|true/false||
|isdate|check if a value is or can be conv…|true/false||
|isdupe|return true if this value was alre…||isdupe([Asset ID]) returns true fo…|
|isformula|check whether a string value is a …|true/false||
|isnum|check if a value is numeric|true/false, or 3-part array||
|isint|check if a value is an integer|true/false||
|instr|finds the position (1-based index)…|offset of location if found (start…||
|join|join values in an array|string of joined values|join(array("a","b","c"))|
|jq|filter|json||
|js|json string|string representation of given obj…||
|json|query of system, report, run or in…|query results, in stringified JSON|json("std_cols"). IMPORTANT NOTE: …|
|jsonesc|escape a string to json format|json-escaped string|jsonesc("abc"def") = "abc\\"def"|
|left|returns a string comprised of the …|||
|length|returns the length (number of char…|||
|ln|natural log|||
|log10|log10|||
|lower|convert to lower case||lower("ABC") returns "abc"|
|map|use mapped value (lookup)|lookup value if found, null otherw…|map(DocType, docTypeDescriptions)|
|map_any|lookup based on either patterns or…|lookup value if matched or equal t…|map_any('FL', 'states') where 'sta…|
|map_reverse|lookup based on the target map val…|first match pattern of matching ta…|map_reverse('FL', 'states') where …|
|match|pattern match|1 if a match is found, else zero. …|match(DocType, "/f.*ll.*doc/" - "/…|
|matchstr|same as match(), but return matche…|matched string|matchstr(DocType, "/f.*ll.*doc/" -…|
|max|return the maximum of a list of va…|||
|median|return the median of a list of num…|||
|mid|returns a substring starting at th…|||
|min|returns the minimum of a list of v…|||
|missing|check if a standard column is defi…|true/false|missing([Current Loan Amount]) ret…|
|mapped|check if the given i) raw column e…||mapped(*.[my raw col]) returns 1 o…|
|msaname|look up msa name from msa number|msa name from given number|msaname(35620) returns "New York-N…|
|msastate|look up name of state from msa num…|state in which the given MSA is lo…|msastate(35620) returns "NY"|
|namedval|deprecated|||
|noerr|returns the value of its argument,…|||
|normalize_id|returns utf8 normalized text (NFKC…|||
|not|logical NOT of the given expression|logical NOT of the given expression|not(3 > 2) returns 0 (false)|
|num2ord|(experimental) convert number into…|"one", "two", "three" etc	||
|nz|non-zero value|first non-zero value|nz(0,0,4,2,0,5) returns 4|
|nzmin|minimum non-zero value||nzmin(0,0,4,2,0,5) returns 2|
|nzmax|maximum non-zero value||nzmax(0,0,4,2,0,5) returns 5|
|!cpr_over_period|CPR over multi-period duration|CPR over multi-period duration|!cpr_over_period() returns yy|
|!ppmt_over_period|(experimental) total sched prin du…|total sched prin due over multi-pe…|!ppmt_over_period(xx) returns yy|
|fv|future value of an investment assu…|future value|fv(0.06/12,2,5995.51,-1000000) ret…|
|nper|number of periods required to pay …|level payment number of periods fo…|nper(.08/12, -734, 100000) returns…|
|obfuscate|obfuscate a value|randomly modifies a value|obfuscate(1234.56) returns a rando…|
|obfuscate_stable|obfuscate a value|randomly modifies a value, returni…|obfuscate_stable(1234.56) returns …|
|pmt|level payment calculation (e.g. fo…|level payment amount for given rat…|pmt(.06/12,360,500000) returns 299…|
|preview_data|provides access to preview data|array of values evaluated on previ…|preview_data([LoanID]) returns an …|
|pow|pow math function|||
|present|Opposite of the missing() function…|true/false|present([Current Loan Amount]) ret…|
|product|calculate the product of two or mo…|product of the arguments. If only …||
|print|prints to stdout (server terminal)…|||
|range|return the index of the bucket in …|integer specifying bucket number w…|range(65000, 2, {25000,50000,75000…|
|rangeformat|return a string representing the b…|bucket range description|rangeformat(65000, 2, {25000,50000…|
|raw|return raw (uncoerced text) value …|raw value|rawuncoerced(cutoff) = *.MyCutoff,…|
|rawcol|reference to tape raw column|value in specified column|rawcol("balance",1,1) returns the …|
|related_rawcols|get related raw column indexes|dictionary, each key is raw column…|related_rawcols([Current Loan Amou…|
|related_stdcols|get related standard column names|array of standard column names|related_stdcolsif([Age] = 0, [Curr…|
|replace|replace value(s) in string|string with replaced value(s)|replace("abcd","bc","hi") returns …|
|replacechars|replace char(s) in string|string with replaced char(s)|replacechars("abcd","cb","hi") ret…|
|replace_match|replace match(es) in string using …|string with replaced value(s)|replace_match("XX5FXXXX", 10, "/([…|
|reset|re-evaluates the first argument's …|the value to which arg1 was set|reset(#my_var) re-evaluates #my_va…|
|reverse|reverse a string or array||reverse("abc") returns "cba"|
|right|get right x chars from value|||
|round|rounds to the given number of deci…|rounded result|round(1.3456, 2) = 1.35|
|setenv|sets an env variable. Options incl…|non-null on error|setenv("mime", ".csv")	|
|stdcol|reference a stdcol by name instead…|||
|stdcol_eq|evaluate if two values are the sam…|-1, 1 or 0 if val1 is less than, e…|stdcol_eq('round2', 1.23, 1.228) r…|
|shorturl|shorten a url. Only works if url t…|shortened url value|shorturl("analyze?abc=def&idn=3") …|
|split|split a string into an array||split("abc,def",",") returns an ar…|
|sqrt|math function|||
|stat|Calculates a statistic, such as a …|value of the calculated statistic,…|stat("wa", ficoorig, balcurr) retu…|
|statbygrp|Calculates a set of statistics, su…|array of values of the calculated …|statbygrp(<ul style='list-style-ty…|
|stream_delim|used to delimit streams. only for …|||
|stringif|shorthand for if x is null then y …|formula1 if value is non-null, els…|stringif("abc",upper([])) returns …|
|text2code|convert common text to codes e.g. …|||
|tostr|cast to string value|||
|sumproduct|calculate the sum-product of two a…|sum of product of arrays|sumproduct({1,2,3},{4,5,6}) = 32|
|sum|calculate the sum of two or more n…|sum of the arguments. If only one …||
|tableval||||
|tobool|convert to bool. if can't convert,…|||
|todate|convert to date, or 0 if can't con…|||
|tonum|convert to number. if input is an …|||
|tonumnull|deprecated. use tonum(x, 1) instea…|||
|trim|trim a string|||
|udate|convert unix timestamp to date|||
|upper|convert to upper case||upper("abc") returns "ABC"|
|urlesc|url-escape a string|||
|url_param|returns the value of the specified…|||
|unformat|clears all formatting, implicit or…||unformat(date(2016,1,1)) returns a…|
|utf|return special utf string. support…||utf(BOM) returns \\xef\\xbb\\xbf	|
|vflip|flips the value of a variable to x…|true if value was set, false other…|vflip(#myvar, 1, 2) sets #myvar to…|
|vset|sets the value of a variable||vset(#myvar, 10) sets #myvar to 10|
|vsetr|sets the reference of a variable||vsetr(#myvar, [abcd]) sets #myvar …|
|with|shorthand for if x is null then y …|formula1 if value is non-null, els…|with("abc",upper([])) returns "ABC…|
|words|split a string into array of words|array of words||
|zipinfo||specified info for a given zip cod…||
|zipmsa||||
|zipstate||state in which the given zip is lo…|zipstate(10019) returns "NY"|
