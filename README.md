# Regular expressions 

```python
import re

dates = ['23-10-2002', '23/10/2002', '23/10/02', '10/23/2002', '23 Oct 2002', '23 October 2002', 'Oct 23, 2002', 'October 23, 2002']
res = [w for w in dates if re.search('\d{2}[/-]\d{2}[/-]\d{2,4}', w)]
print(res)
# ['23-10-2002', '23/10/2002', '23/10/02', '10/23/2002']

dates_str = '\n'.join(dates)
# '23-10-2002\n23/10/2002\n23/10/02\n10/23/2002\n23 Oct 2002\n23 October 2002\nOct 23, 2002\nOctober 23, 2002'
res = re.findall(r'\d{2} (Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec) \d{4}', dates_str)
print(res)
# ['Oct']
res = re.findall(r'\d{2} (?:Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec) \d{4}', dates_str)
print(res)
# ['23 Oct 2002']
res = re.findall(r'\d{2} (?:Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)[a-z]* \d{4}', dates_str)
print(res)
# ['23 Oct 2002', '23 October 2002']
res = re.findall(r'(?:\d{1,2} )?(?:Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)[a-z]* (?:\d{1,2}, )?\d{4}', dates_str)
print(res)
# ['23 Oct 2002', '23 October 2002', 'Oct 23, 2002', 'October 23, 2002']

soup = BeautifulSoup(source, "html.parser")
message_elements = soup.findAll('tr', id=re.compile("^message_\d+"))
id_tag = soup.find('a', href=re.compile('index.php\?user_id=\d+'))

words = re.sub("[{}]", "", words)
words = re.sub("(\\|[^ ]+)", "", words)
words = re.sub("\\?", "", words)
words = re.sub("\xc2\xa0+", " ", words)
words = words.translate(None, string.punctuation)
words = re.sub("\\s+", " ", words)


names = 'John G. +      Ann H. +        Hanna Y. -      Zack B. *       Elle S. +'
splitted_names = re.compile("(?<=[\\+|\\-|\\*])\s+").split(names)
print(splitted_names)
# ['John G. +', 'Ann H. +', 'Hanna Y. -', 'Zack B. *', 'Elle S. +']

attribute_value = 'attribute_name=23'
res = re.compile("(?<=[=])\d{1,2}$").split(attribute_value)
print(res)
# ['attribute_name=', '']

res = re.search('(?<=abc)def', 'abcdef')
print(res.group(0))
# def

res = re.search('(?<=-)\w+', 'spam-egg')
print(res.group(0))
# egg

res = re.findall(r'\w+day\b', "Monday: The doctor's appointment is at 2:45pm.")
print(res)
# ['Monday']
res = re.findall(r'\w+day\b', "MondayG: The doctor's appointment is at 2:45pm.")
print(res)
# []
res = re.search(r'(\w+day\b)', "Monday: The doctor's appointment is at 2:45pm.").group()
print(res)
# Monday
```

# Regular expressions with Pandas

```python
import pandas as pd

time_sentences = ["Monday: The doctor's appointment is at 2:45pm.", 
                  "Tuesday: The dentist's appointment is at 11:30 am.",
                  "Wednesday: At 7:00pm, there is a basketball game!",
                  "Thursday: Be back home by 11:15 pm at the latest.",
                  "Friday: Take the train at 08:10 am, arrive at 09:00am."]

df = pd.DataFrame(time_sentences, columns=['text'])

# find the number of characters for each string in df['text']
df['text'].str.len()

# find the number of tokens for each string in df['text']
df['text'].str.split().str.len()

# find which entries contain the word 'appointment'
df['text'].str.contains('appointment')

# find how many times a digit occurs in each string
df['text'].str.count(r'\d')

# find all occurances of the digits
df['text'].str.findall(r'\d')

# group and find the hours and minutes
df['text'].str.findall(r'(\d?\d):(\d\d)')

# replace weekdays with '???'
df['text'].str.replace(r'\w+day\b', '???')

# replace weekdays with 3 letter abbrevations
df['text'].str.replace(r'(\w+day\b)', lambda x: x.groups()[0][:3])

# create new columns from first match of extracted groups
df['text'].str.extract(r'(\d?\d):(\d\d)')

# extract the entire time, the hours, the minutes, and the period
df['text'].str.extractall(r'((\d?\d):(\d\d) ?([ap]m))')

# extract the entire time, the hours, the minutes, and the period with group names
df['text'].str.extractall(r'(?P<time>(?P<hour>\d?\d):(?P<minute>\d\d) ?(?P<period>[ap]m))')
```