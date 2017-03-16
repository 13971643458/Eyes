�ؼ��֣�״̬����ɫ  ͸��״̬��  ����ʽ �׵׺��� 
���͵�ַ��[Android�޸�״̬����ɫȫ��λ�̳�](http://www.jianshu.com/p/932568ed31af)
�ο����£�
[Android-transulcent-status-bar](http://niorgai.github.io/2016/03/20/Android-transulcent-status-bar/)
[Android 6.0״̬��ʹ�û�ɫ���ֺ�ͼ��](https://www.aswifter.com/2015/12/24/android-m-change-statusbar-textcolor/)
[Androidϵͳ����״̬��������ɫ](http://blog.isming.me/2016/01/09/chang-android-statusbar-text-color/)

�ڹȸ�ٷ���material����ĵ��ж������µ�״̬����ơ�
[https://material.io/guidelines/layout/structure.html#structure-system-bars](https://material.io/guidelines/layout/structure.html#structure-system-bars)

Ĭ�������,״̬������ɫ�Ǻ�ɫ�ġ�ͬʱ״̬����ɫҲ���԰�͸������ָ������һ����ɫ��
1.�ı���ɫ���״̬��

![](http://upload-images.jianshu.io/upload_images/5022380-2f9c63416ead386b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/5022380-04f743737df89900.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.��͸��״̬��

![](http://upload-images.jianshu.io/upload_images/5022380-c06f5e24e2e8c5d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.��ɫ״̬��

![](http://upload-images.jianshu.io/upload_images/5022380-03b4b8342022338a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


��ɫicon�����ֵ�״̬��

![](http://upload-images.jianshu.io/upload_images/5022380-d8809c0210144872.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/5022380-aee082fe5dc42abd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/5022380-09d1b99ebfb12811.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/5022380-60055e8ee00cddd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
��������һ�¾���ʵ��
<br />
##һ.�ı�״̬����ɫ

**4.4-5.0�Ĵ���**
4.4-5.0��û��API����ֱ���޸�״̬����ɫ�����Ա����Ƚ�״̬������Ϊ͸����Ȼ���ڲ��������һ������Ϊ����ɫֵ��View����Ϊ״̬������䡣

```
Window window = activity.getWindow();
//����WindowΪȫ͸��
window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);

ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
//��ȡ������
View mContentChild = mContentView.getChildAt(0);
//��ȡ״̬���߶�
int statusBarHeight = getStatusBarHeight(activity);

//����Ѿ����ڼ�״̬�����Ƴ�����ֹ�ظ����
removeFakeStatusBarViewIfExist(activity);
//һ��View����Ϊ״̬�������
addFakeStatusBarView(activity, statusColor, statusBarHeight);
//�����ӿؼ���״̬���ļ��
addMarginTopToContentChild(mContentChild, statusBarHeight);
//��Ԥ��ϵͳ��λ��
if (mContentChild != null) {
	ViewCompat.setFitsSystemWindows(mContentChild, false);
}
//�����Activity��ʹ����ActionBar����Ҫ�ٽ�������״̬���ĸ߶ȵ���һ��ActionBar�ĸ߶ȣ��������ݻᱻActionBar�ڵ�
int action_bar_id = activity.getResources().getIdentifier("action_bar", "id", activity.getPackageName());
View view = activity.findViewById(action_bar_id);
if (view != null) {
	if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT && Build.VERSION.SDK_INT <= Build.VERSION_CODES.LOLLIPOP) {
		TypedValue typedValue = new TypedValue();
		if (activity.getTheme().resolveAttribute(R.attr.actionBarSize, typedValue, true)) {
			int actionBarHeight = TypedValue.complexToDimensionPixelSize(typedValue.data, activity.getResources().getDisplayMetrics());
			Eyes.setContentTopPadding(activity, actionBarHeight);
		}
	}
}
```
```
private static void removeFakeStatusBarViewIfExist(Activity activity) {
	Window window = activity.getWindow();
	ViewGroup mDecorView = (ViewGroup) window.getDecorView();

	View fakeView = mDecorView.findViewWithTag(TAG_FAKE_STATUS_BAR_VIEW);
	if (fakeView != null) {
		mDecorView.removeView(fakeView);
	}
}
```
```
private static View addFakeStatusBarView(Activity activity, int statusBarColor, int statusBarHeight) {
	Window window = activity.getWindow();
	ViewGroup mDecorView = (ViewGroup) window.getDecorView();

	View mStatusBarView = new View(activity);
	FrameLayout.LayoutParams layoutParams = new FrameLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, statusBarHeight);
	layoutParams.gravity = Gravity.TOP;
	mStatusBarView.setLayoutParams(layoutParams);
	mStatusBarView.setBackgroundColor(statusBarColor);
	mStatusBarView.setTag(TAG_FAKE_STATUS_BAR_VIEW);

	mDecorView.addView(mStatusBarView);
	return mStatusBarView;
}
```
```
private static void addMarginTopToContentChild(View mContentChild, int statusBarHeight) {
	if (mContentChild == null) {
		return;
	}
	if (!TAG_MARGIN_ADDED.equals(mContentChild.getTag())) {
		FrameLayout.LayoutParams lp = (FrameLayout.LayoutParams) mContentChild.getLayoutParams();
		lp.topMargin += statusBarHeight;
		mContentChild.setLayoutParams(lp);
		mContentChild.setTag(TAG_MARGIN_ADDED);
	}
}
```

**Android5.0���ϵĴ���**
```
Window window = activity.getWindow();
//ȡ��״̬��͸��
window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
//���Flag��״̬����Ϊ�ɻ���ģʽ
window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
//����״̬����ɫ
window.setStatusBarColor(statusColor);
//����ϵͳ״̬�����ڿɼ�״̬
window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);
//��view������ϵͳ�����������Լ��Ĳ���
ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
View mChildView = mContentView.getChildAt(0);
if (mChildView != null) {
	ViewCompat.setFitsSystemWindows(mChildView, false);
	ViewCompat.requestApplyInsets(mChildView);
}
```
<br />
##��.͸��״̬��

**4.4-5.0�Ĵ���**
```
Window window = activity.getWindow();
//����WindowΪ͸��
window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);

ViewGroup mContentView = (ViewGroup) activity.findViewById(Window.ID_ANDROID_CONTENT);
View mContentChild = mContentView.getChildAt(0);

//�Ƴ��Ѿ����ڵļ�״̬��,����ȡ������Margin���
removeFakeStatusBarViewIfExist(activity);
removeMarginTopOfContentChild(mContentChild, getStatusBarHeight(activity));
if (mContentChild != null) {
	//fitsSystemWindow Ϊ false, ��Ԥ��ϵͳ��λ��.
	ViewCompat.setFitsSystemWindows(mContentChild, false);
}
```

**5.0���ϵĴ���**
```
Window window = activity.getWindow();
//���Flag��״̬����Ϊ�ɻ���ģʽ
window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
if (hideStatusBarBackground) {
	//���Ϊȫ͸��ģʽ��ȡ������Window��͸����Flag
	window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
	//����״̬��Ϊ͸��
	window.setStatusBarColor(Color.TRANSPARENT);
	//����window��״̬�����ɼ�
	window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_STABLE | View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN);
} else {
	//���Ϊ��͸��ģʽ���������Window��͸����Flag
	window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
	//����ϵͳ״̬�����ڿɼ�״̬
	window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);
}
//view������ϵͳ�����������Լ��Ĳ���
ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
View mChildView = mContentView.getChildAt(0);
if (mChildView != null) {
	ViewCompat.setFitsSystemWindows(mChildView, false);
	ViewCompat.requestApplyInsets(mChildView);
}
```
<br />
##��.ʹ��CollapsingToolbarLayoutʹToolBar�����۵�Ч��
![](http://upload-images.jianshu.io/upload_images/5022380-39308f8e41c54a90.gif?imageMogr2/auto-orient/strip)
����ͼƬ�е�Ч��
**4.4-5.0�Ĵ���**
```
Window window = activity.getWindow();
//����WindowΪȫ͸��
window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);

//AppBarLayout,CollapsingToolbarLayout,ToolBar,ImageView��fitsSystemWindowͳһ��Ϊfalse, ��Ԥ��ϵͳ��λ��.
View mContentChild = mContentView.getChildAt(0);
mContentChild.setFitsSystemWindows(false);
((View) appBarLayout.getParent()).setFitsSystemWindows(false);
appBarLayout.setFitsSystemWindows(false);
collapsingToolbarLayout.setFitsSystemWindows(false);
collapsingToolbarLayout.getChildAt(0).setFitsSystemWindows(false);

toolbar.setFitsSystemWindows(false);
//ΪToolbar���һ��״̬���ĸ߶�, ͬʱΪToolbar���paddingTop,ʹToolbar����״̬����ToolBar��title����������ʾ.
if (toolbar.getTag() == null) {
	CollapsingToolbarLayout.LayoutParams lp = (CollapsingToolbarLayout.LayoutParams) toolbar.getLayoutParams();
	int statusBarHeight = getStatusBarHeight(activity);
	lp.height += statusBarHeight;
	toolbar.setLayoutParams(lp);
	toolbar.setPadding(toolbar.getPaddingLeft(), toolbar.getPaddingTop() + statusBarHeight, toolbar.getPaddingRight(), toolbar.getPaddingBottom());
	toolbar.setTag(true);
}
//�Ƴ��Ѿ����ڼ�״̬����,����ȡ������Margin���
int statusBarHeight = getStatusBarHeight(activity);
removeFakeStatusBarViewIfExist(activity);
removeMarginTopOfContentChild(mContentChild, statusBarHeight);
//���һ��View����Ϊ״̬�������
final View statusView = addFakeStatusBarView(activity, statusColor, statusBarHeight);

CoordinatorLayout.Behavior behavior = ((CoordinatorLayout.LayoutParams) appBarLayout.getLayoutParams()).getBehavior();
if (behavior != null && behavior instanceof AppBarLayout.Behavior) {
	int verticalOffset = ((AppBarLayout.Behavior) behavior).getTopAndBottomOffset();
	if (Math.abs(verticalOffset) > appBarLayout.getHeight() - collapsingToolbarLayout.getScrimVisibleHeightTrigger()) {
		statusView.setAlpha(1f);
	} else {
		statusView.setAlpha(0f);
	}
} else {
	statusView.setAlpha(0f);
}
appBarLayout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
	@Override
	public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
		if (Math.abs(verticalOffset) > appBarLayout.getHeight() - collapsingToolbarLayout.getScrimVisibleHeightTrigger()) {
			//toolbar���۵�ʱ��ʾ״̬��
			if (statusView.getAlpha() == 0) {
				statusView.animate().cancel();
				statusView.animate().alpha(1f).setDuration(collapsingToolbarLayout.getScrimAnimationDuration()).start();
			}
		} else {
			//toolbarչ��ʱ��ʾ״̬��
			if (statusView.getAlpha() == 1) {
				statusView.animate().cancel();
				statusView.animate().alpha(0f).setDuration(collapsingToolbarLayout.getScrimAnimationDuration()).start();
			}
		}
	}
});
```

**5.0���ϵĴ���**
```
final Window window = activity.getWindow();
//ȡ������Window��͸����Flag
window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
////���Flag��״̬����Ϊ�ɻ���ģʽ
window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
//����״̬��Ϊ͸��
window.setStatusBarColor(Color.TRANSPARENT);
//����ϵͳ״̬�����ڿɼ�״̬
window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);
//ͨ��OnApplyWindowInsetsListener()ʹLayout�ڻ��ƹ����н�View����ƫ����,ʹcollapsingToolbarLayout����ռ��״̬��
ViewCompat.setOnApplyWindowInsetsListener(collapsingToolbarLayout, new OnApplyWindowInsetsListener() {
	@Override
	public WindowInsetsCompat onApplyWindowInsets(View v, WindowInsetsCompat insets) {
		return insets;
	}
});

ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
View mChildView = mContentView.getChildAt(0);
//view������ϵͳ�����������Լ��Ĳ���
if (mChildView != null) {
	ViewCompat.setFitsSystemWindows(mChildView, false);
	ViewCompat.requestApplyInsets(mChildView);
}

((View) appBarLayout.getParent()).setFitsSystemWindows(false);
appBarLayout.setFitsSystemWindows(false);

toolbar.setFitsSystemWindows(false);
//ΪToolbar���һ��״̬���ĸ߶�, ͬʱΪToolbar���paddingTop,ʹToolbar����״̬����ToolBar��title����������ʾ.
if (toolbar.getTag() == null) {
	CollapsingToolbarLayout.LayoutParams lp = (CollapsingToolbarLayout.LayoutParams) toolbar.getLayoutParams();
	int statusBarHeight = getStatusBarHeight(activity);
	lp.height += statusBarHeight;
	toolbar.setLayoutParams(lp);
	toolbar.setPadding(toolbar.getPaddingLeft(), toolbar.getPaddingTop() + statusBarHeight, toolbar.getPaddingRight(), toolbar.getPaddingBottom());
	toolbar.setTag(true);
}

CoordinatorLayout.Behavior behavior = ((CoordinatorLayout.LayoutParams) appBarLayout.getLayoutParams()).getBehavior();
if (behavior != null && behavior instanceof AppBarLayout.Behavior) {
	int verticalOffset = ((AppBarLayout.Behavior) behavior).getTopAndBottomOffset();
	if (Math.abs(verticalOffset) > appBarLayout.getHeight() - collapsingToolbarLayout.getScrimVisibleHeightTrigger()) {
		window.setStatusBarColor(statusColor);
	} else {
		window.setStatusBarColor(Color.TRANSPARENT);
	}
} else {
	window.setStatusBarColor(Color.TRANSPARENT);
}

collapsingToolbarLayout.setFitsSystemWindows(false);
appBarLayout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
	@Override
	public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
		if (Math.abs(verticalOffset) > appBarLayout.getHeight() - collapsingToolbarLayout.getScrimVisibleHeightTrigger()) {
			//toolbar���۵�ʱ��ʾ״̬��
			if (window.getStatusBarColor() == Color.TRANSPARENT) {
				ValueAnimator animator = ValueAnimator.ofArgb(Color.TRANSPARENT, statusColor)
						.setDuration(collapsingToolbarLayout.getScrimAnimationDuration());
				animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
					@Override
					public void onAnimationUpdate(ValueAnimator valueAnimator) {
						window.setStatusBarColor((Integer) valueAnimator.getAnimatedValue());
					}
				});
				animator.start();
			}
		} else {
			//toolbar��ʾʱͬʱ��ʾ״̬��
			if (window.getStatusBarColor() == statusColor) {
				ValueAnimator animator = ValueAnimator.ofArgb(statusColor, Color.TRANSPARENT)
						.setDuration(collapsingToolbarLayout.getScrimAnimationDuration());
				animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
					@Override
					public void onAnimationUpdate(ValueAnimator valueAnimator) {
						window.setStatusBarColor((Integer) valueAnimator.getAnimatedValue());
					}
				});
				animator.start();
			}
		}
	}
});
collapsingToolbarLayout.getChildAt(0).setFitsSystemWindows(false);
//����״̬������ɫ
collapsingToolbarLayout.setStatusBarScrimColor(statusColor);
```
<br />
##��.����״̬��������ɫ
��Android 6.0��Api���ṩ��SYSTEM_UI_FLAG_LIGHT_STATUS_BAR��ôһ������������ʹ״̬����������Ϊ��ɫ������6.0�����ǲ������õġ�
С�׺�������ֻ�Ҳ���Դﵽ���Ч����Ҫ��һЩ����Ĵ������Բο�С�׺�����Ŀ������ĵ���
[Flyme����ʽ״̬��](http://open-wiki.flyme.cn/index.php?title=Flyme%E7%B3%BB%E7%BB%9FAPI#.E4.B8.80.E3.80.81.E6.B2.89.E6.B5.B8.E5.BC.8F.E7.8A.B6.E6.80.81.E6.A0.8F)
[MIUI 6 ����ʽ״̬�����÷���](http://dev.xiaomi.com/doc/p=4769/)

```
 if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
	//�ж��Ƿ�ΪС�׻������ֻ����������״̬�����ָ�Ϊ��ɫ
	if (MIUISetStatusBarLightMode(activity, true) || FlymeSetStatusBarLightMode(activity, true)) {
		//����״̬��Ϊָ����ɫ
		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {//5.0
			activity.getWindow().setStatusBarColor(color);
		} else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {//4.4
			setStatusBarColor(activity, color);
		}
	} else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
		//�����6.0���Ͻ�״̬�����ָ�Ϊ��ɫ��������״̬����ɫ
		activity.getWindow().setBackgroundDrawableResource(android.R.color.transparent);
		activity.getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN | View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR);
		activity.getWindow().setStatusBarColor(color);

		//fitsSystemWindow Ϊ false, ��Ԥ��ϵͳ��λ��.
		ViewGroup mContentView = (ViewGroup) activity.getWindow().findViewById(Window.ID_ANDROID_CONTENT);
		View mChildView = mContentView.getChildAt(0);
		if (mChildView != null) {
			ViewCompat.setFitsSystemWindows(mChildView, true);
			ViewCompat.requestApplyInsets(mChildView);
		}
	} else {
		//��������޷�������״̬����Ϊ��ɫ
		setStatusBarColor(activity, Color.BLACK);
	}
}
```

```
static boolean MIUISetStatusBarLightMode(Activity activity, boolean darkmode) {
	boolean result = false;
	Class<? extends Window> clazz = activity.getWindow().getClass();
	try {
		int darkModeFlag = 0;
		Class<?> layoutParams = Class.forName("android.view.MiuiWindowManager$LayoutParams");
		Field field = layoutParams.getField("EXTRA_FLAG_STATUS_BAR_DARK_MODE");
		darkModeFlag = field.getInt(layoutParams);
		Method extraFlagField = clazz.getMethod("setExtraFlags", int.class, int.class);
		extraFlagField.invoke(activity.getWindow(), darkmode ? darkModeFlag : 0, darkModeFlag);
		result = true;
	} catch (Exception e) {
		e.printStackTrace();
	}
	return result;
}
```
```
static boolean FlymeSetStatusBarLightMode(Activity activity, boolean darkmode) {
boolean result = false;
try {
	WindowManager.LayoutParams lp = activity.getWindow().getAttributes();
	Field darkFlag = WindowManager.LayoutParams.class
			.getDeclaredField("MEIZU_FLAG_DARK_STATUS_BAR_ICON");
	Field meizuFlags = WindowManager.LayoutParams.class
			.getDeclaredField("meizuFlags");
	darkFlag.setAccessible(true);
	meizuFlags.setAccessible(true);
	int bit = darkFlag.getInt(null);
	int value = meizuFlags.getInt(lp);
	if (darkmode) {
		value |= bit;
	} else {
		value &= ~bit;
	}
	meizuFlags.setInt(lp, value);
	activity.getWindow().setAttributes(lp);
	result = true;
} catch (Exception e) {
	e.printStackTrace();
}
return result;
}
```
</br>
