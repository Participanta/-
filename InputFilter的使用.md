#InputFilter的使用
---
### 参数说明
```
/**
 *
 * @param source
 *            输入的文字
 * @param start
 *            开始位置
 * @param end
 *            结束位置
 * @param dest
 *            当前显示的内容
 * @param dstart
 *            当前开始位置
 * @param dend
 *            当前结束位置
 * @return
 */
 public CharSequence filter(CharSequence source, int start, int end,
 								   Spanned dest, int dstart, int dend);
```

### 使用示例

/**
**输入强制转换20位大写字母**  
**/
```
public class MyFilter implements InputFilter{

    private final int MAX_LENGTH = 20;  //设置最大长度
    @Override
    public CharSequence filter(CharSequence source, int start, int end,
                               Spanned dest, int dstart, int dend) {
        int inputMax = MAX_LENGTH - (dest.length() - (dend - dstart));

        boolean hasLowerCase = false;
        boolean over = false;
        int count = 0;
        int index = end;
        for (int i = start; i < end; i++) {
            if (count >= inputMax) {
                index = i;
                over = true;
                break;
            }
            char ch = source.charAt(i);
            if (charIsLowerCase(ch)) {
                hasLowerCase = true;
            } else if (!charIsUpperCase(ch) && !charIsNum(ch)) {
                char[] v = new char[i - start];
                TextUtils.getChars(source, start, i, v, 0);
                String s = new String(v).toUpperCase();

                if (source instanceof Spanned) {
                    SpannableString sp = new SpannableString(s);
                    TextUtils.copySpansFrom((Spanned) source, start, i,
                            null, sp, 0);
                    return sp;
                } else {
                    return s;
                }
            }
            count++;
        }

        if (hasLowerCase) {
            char[] v = new char[index - start];
            TextUtils.getChars(source, start, index, v, 0);
            String s = new String(v).toUpperCase();

            if (source instanceof Spanned) {
                SpannableString sp = new SpannableString(s);
                TextUtils.copySpansFrom((Spanned) source, start, index,
                        null, sp, 0);
                return sp;
            } else {
                return s;
            }
        } else if (over) {
            char[] v = new char[index - start];
            TextUtils.getChars(source, start, index, v, 0);
            String s = new String(v);

            if (source instanceof Spanned) {
                SpannableString sp = new SpannableString(s);
                TextUtils.copySpansFrom((Spanned) source, start, index,
                        null, sp, 0);
                return sp;
            } else {
                return s;
            }
        }

        return null;
    }

    /**
     * 数字 (0-9)
     *
     * @param ch
     * @return
     */
    private boolean charIsNum(char input) {
        return ('0' <= input) && ('9' >= input);
    }

    /**
     * 小写字母 (a-z)
     *
     * @param ch
     * @return
     */
    private boolean charIsLowerCase(char input) {
        return ('a' <= input) && ('z' >= input);
    }

    /**
     * 大写字母 (A-Z)
     *
     * @param ch
     * @return
     */
    private boolean charIsUpperCase(char input) {
        return ('A' <= input) && ('Z' >= input);
    }
}
```
