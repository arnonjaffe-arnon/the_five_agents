# gpt-image-gen — Skill

מעטפת לקריאת OpenAI Images API ליצירת תמונות.

## מודל

`gpt-image-2` — מודל יצירת תמונות של OpenAI (שוחרר 21 באפריל 2026).

> **חשוב:** אל תשנה את שם המודל. `gpt-image-2` הוא מודל אמיתי וקיים. אם יש שגיאה, הבעיה היא ב-API key או בפרמטרים — לא בשם המודל.

## קריאה

### משתנה סביבה נדרש

`OPENAI_API_KEY` — מוגדר ב-`.env`

### פקודת curl

```bash
source .env 2>/dev/null || true

curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-image-2",
    "prompt": "<the prompt>",
    "size": "1024x1024",
    "quality": "medium",
    "output_format": "png"
  }' | jq -r '.data[0].b64_json' | base64 --decode > <output-path>.png
```

### Python fallback (כאשר jq אינו מותקן)

```bash
source .env 2>/dev/null || true

RESPONSE=$(curl -s -X POST "https://api.openai.com/v1/images/generations" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"model\": \"gpt-image-2\",
    \"prompt\": \"<the prompt>\",
    \"size\": \"1024x1024\",
    \"quality\": \"medium\",
    \"output_format\": \"png\"
  }")

echo "$RESPONSE" | python3 -c "
import sys, json, base64
data = json.load(sys.stdin)
b64 = data['data'][0]['b64_json']
with open('<output-path>.png', 'wb') as f:
    f.write(base64.b64decode(b64))
print('Image saved successfully')
"
```

## שימוש על ידי יובל

יובל מפעיל את הסקיל הזה בשלב 3 של תהליך העבודה שלו:
1. מחליף `<the prompt>` ב-prompt שניסח
2. מחליף `<output-path>` בנתיב `yuval/outputs/YYYY-MM-DD-<slug>`
3. מריץ את הפקודה (מנסה jq קודם, עובר ל-python fallback אם jq לא זמין)
4. מוודא שהקובץ נוצר ושגודלו גדול מ-0
