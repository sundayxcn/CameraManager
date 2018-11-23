# cameramanager
用于快速接入camera预览
## 注意事项
1. 在有预览界面的activity使用rxpermission申请camera权限在部分手机上权限弹窗弹不出来
2. camera的打开和释放跟着surfaceView走，不需要额外在onresume和onpause操作

## 导入方式
第一步：
工程根目录中build.gradle中仓库地址增加maven jitpack
```
allprojects {
    repositories {
        jcenter()
        repositories {
            maven { url 'https://www.jitpack.io' }
        }
    }
}
```
第二步：
模块build.gradle中引入
```
dependencies {
   implementation 'com.github.sundayxcn:cameramanager:1.3'
}

```
```java
    
    public static final int REQUEST_CODE_CAMERA = 999;
    private SurfaceView mSurfaceView;
    private CameraManager cameraManager;
    //预览图的仓库，已包含一个默认的仓库，可复写接口PreviewRepertory自定义
    private PreviewRepertory previewRepertory = new FacePreviewRepertory();

    @RequiresApi(api = Build.VERSION_CODES.M)
    @Override
    protected void onCreate( Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_camera);
        mSurfaceView = findViewById(R.id.surface_view);
        if(ContextCompat.checkSelfPermission(this,Manifest.permission.CAMERA)!=0) {
            requestPermissions(new String[]{Manifest.permission.CAMERA}, REQUEST_CODE_CAMERA);
        }else{
            cameraManager = new CameraManager.Builder(mSurfaceView,previewRepertory).build();
        }
    }


    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        //super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        if(permissions.length > 0){
            for(String permission : permissions){
                if(permission.equals(Manifest.permission.CAMERA)){
                    cameraManager = new CameraManager.Builder(mSurfaceView,previewRepertory).build();
                    //这里必须open
                    cameraManager.openCamera();
                }else{
                    Toast.makeText(CameraActivity.this,"没有权限",Toast.LENGTH_SHORT).show();
                    finish();
                }
            }
        }
    }