# 【Android 自定义View 字母选择器】

```java
public class LetterView extends View {
    /** 字母表字符串，将以","分割 */
    private String mLetterString="";
    /** 最终索引的字符串数组 */
    private String[] mLetters = null;
    private int pressedIndex = -1;

    private int mLetterColor = Color.RED;
    private int mLetterColorPressed = mLetterColor;
    private int mLetterSize = 0;
    private int mOrientation = VERTICAL;
    private static final int HORIZONTAL = 0;
    private static final int VERTICAL = 1;


    private TextPaint mTextPaint;
    private int singleWidth, singleHeight;
    private OnLetterPressedListener onLetterPressedListener;
    private Rect bounds;
    private Paint.FontMetrics fontMetrics;

    public LetterView(Context context) {
        super(context);
        init(null, 0);
    }

    public LetterView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(attrs, 0);
    }

    public LetterView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        init(attrs, defStyle);
    }

    private void init(AttributeSet attrs, int defStyle) {
        // Load attributes
        final TypedArray a = getContext().obtainStyledAttributes(
                attrs, R.styleable.LetterView, defStyle, 0);

        mLetterString = a.getString(
                R.styleable.LetterView_letterString);
        if (!TextUtils.isEmpty(mLetterString) && mLetterString.contains(",")) {
            mLetters = mLetterString.split(",");
        }

        mLetterColor = a.getColor(
                R.styleable.LetterView_letterColor,
                mLetterColor);
        mLetterColorPressed = a.getColor(
                R.styleable.LetterView_letterColorPressed,
                mLetterColorPressed);
        // Use getDimensionPixelSize or getDimensionPixelOffset when dealing with
        // values that should fall on pixel boundaries.
        mLetterSize = a.getDimensionPixelSize(
                R.styleable.LetterView_letterSize,
                mLetterSize);
        mOrientation = a.getInt(R.styleable.LetterView_orientation, mOrientation);

        a.recycle();

        // Set up a default TextPaint object
        mTextPaint = new TextPaint();
        mTextPaint.setFlags(Paint.ANTI_ALIAS_FLAG);
        mTextPaint.setTextAlign(Paint.Align.CENTER);
        fontMetrics = mTextPaint.getFontMetrics();
        bounds = new Rect();
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        if (mLetters == null || mLetters.length == 0) {
            return;
        }

        // allocations per draw cycle.
        int paddingLeft = getPaddingLeft();
        int paddingTop = getPaddingTop();
        int paddingRight = getPaddingRight();
        int paddingBottom = getPaddingBottom();
        int contentWidth = getWidth() - paddingLeft - paddingRight;
        int contentHeight = getHeight() - paddingTop - paddingBottom;
        int letterWidth = 0, letterHeight = 0;

        for (int i = 0; i < mLetters.length; i++) {
            if (i == pressedIndex) {
                mTextPaint.setColor(mLetterColorPressed);
            } else {
                mTextPaint.setColor(mLetterColor);
            }
            mTextPaint.setTextSize(mLetterSize);

            float xPos, yPos;

            if (mOrientation == HORIZONTAL) {
                singleWidth = contentWidth / mLetters.length;

                mTextPaint.getTextBounds(mLetters[i], 0, mLetters[i].length(), bounds);
                letterWidth = bounds.width();
                while (letterWidth > singleWidth) {
                    mLetterSize--;
                    mTextPaint.setTextSize(mLetterSize);
                    letterWidth = (int) mTextPaint.measureText(mLetters[i]);
                }

                fontMetrics = mTextPaint.getFontMetrics();
                letterHeight = (int) Math.ceil(fontMetrics.descent - fontMetrics.ascent);
                while (letterHeight > contentHeight) {
                    mLetterSize--;
                    mTextPaint.setTextSize(mLetterSize);
                    fontMetrics = mTextPaint.getFontMetrics();
                    letterHeight = (int) Math.ceil(fontMetrics.descent - fontMetrics.ascent);
                }
                letterWidth = (int) mTextPaint.measureText(mLetters[i]);

                xPos = paddingLeft + singleWidth * (i + 0.5f);
                yPos = paddingTop + (contentHeight - letterHeight) / 2 - fontMetrics.ascent;

            } else {
                singleHeight = contentHeight / mLetters.length;

                fontMetrics = mTextPaint.getFontMetrics();
                letterHeight = (int) Math.ceil(fontMetrics.descent - fontMetrics.ascent);
                while (letterHeight > singleHeight) {
                    mLetterSize--;
                    mTextPaint.setTextSize(mLetterSize);
                    fontMetrics = mTextPaint.getFontMetrics();
                    letterHeight = (int) Math.ceil(fontMetrics.descent - fontMetrics.ascent);
                }

                letterWidth = (int) mTextPaint.measureText(mLetters[i]);
                while (letterWidth > contentWidth) {
                    mLetterSize--;
                    mTextPaint.setTextSize(mLetterSize);
                    letterWidth = (int) mTextPaint.measureText(mLetters[i]);
                }
                fontMetrics = mTextPaint.getFontMetrics();
                letterHeight = (int) Math.ceil(fontMetrics.descent - fontMetrics.ascent);

                xPos = paddingLeft + contentWidth / 2;
                yPos = paddingTop + singleHeight * i + (singleHeight - letterHeight) / 2 - fontMetrics.ascent;
            }

            /* x默认是这个字符串的左边在屏幕的位置，
               如果设置了paint.setTextAlign(Paint.Align.CENTER)，那就是字符的中心；
               y是指定这个字符baseline在屏幕上的位置 */
            canvas.drawText(mLetters[i], xPos, yPos, mTextPaint);
        }

        if (mOrientation == HORIZONTAL) {
            this.measure(getWidth(), paddingTop + paddingBottom + letterHeight);
        }
    }

    @Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        if (mLetters == null || mLetters.length == 0) {
            return false;
        }

        int index = -1;
        if (mOrientation == HORIZONTAL) {
            if (singleWidth > 0) {
                index = (int) ((event.getX() - getPaddingLeft()) / singleWidth);
            }
        } else {
            if (singleHeight > 0) {
                index = (int) ((event.getY() - getPaddingTop()) / singleHeight);
            }
        }

        int oldIndex = pressedIndex;
        OnLetterPressedListener listener = onLetterPressedListener;
        switch (event.getAction()) {
            case MotionEvent.ACTION_DOWN:
                if (index != oldIndex && listener != null
                        && index >= 0 && index < mLetters.length) {
                    listener.onLetterPressed(mLetters[index]);
                    pressedIndex = index;
                    invalidate();
                }
                break;

            case MotionEvent.ACTION_MOVE:
                if (index != oldIndex && listener != null
                        && index >= 0 && index < mLetters.length) {
                    listener.onLetterPressed(mLetters[index]);
                    pressedIndex = index;
                    invalidate();
                }
                break;

            case MotionEvent.ACTION_UP:
                pressedIndex = -1;
                invalidate();
                break;

            default:
                break;
        }


        return true;
    }

    public interface OnLetterPressedListener {
        public void onLetterPressed(String letter);
    }

    public void setOnLetterPressedListener(
            OnLetterPressedListener onLetterPressedListener) {
        this.onLetterPressedListener = onLetterPressedListener;
    }


    /**
     * Gets the example string attribute value.
     *
     * @return The example string attribute value.
     */
    public String getLetterString() {
        return mLetterString;
    }

    /**
     * Sets the view's example string attribute value. In the example view, this string
     * is the text to draw.
     *
     * @param LetterString The example string attribute value to use.
     */
    public void setLetterString(String LetterString) {
        mLetterString = LetterString;
        invalidate();
    }

    /**
     * Gets the example color attribute value.
     *
     * @return The example color attribute value.
     */
    public int getLetterColor() {
        return mLetterColor;
    }

    /**
     * Sets the view's example color attribute value. In the example view, this color
     * is the font color.
     *
     * @param LetterColor The example color attribute value to use.
     */
    public void setLetterColor(int LetterColor) {
        mLetterColor = LetterColor;
        invalidate();
    }

    /**
     * Gets the example dimension attribute value.
     *
     * @return The example dimension attribute value.
     */
    public float getLetterSize() {
        return mLetterSize;
    }

    /**
     * Sets the view's example dimension attribute value. In the example view, this dimension
     * is the font size.
     *
     * @param LetterSize The example dimension attribute value to use.
     */
    public void setLetterSize(int LetterSize) {
        mLetterSize = LetterSize;
        invalidate();
    }
}
```

