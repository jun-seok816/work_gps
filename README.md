# work_gps
- GpsTracker
## MainActivity
> Description

- MainActivity.java 파일에서는 위치 접근 권한과 GPS 사용 가능 여부를 체크해서 처리합니다. 
- 버튼을 클릭하면 위치 정보를 가져오기 위해 GpsTracker 서비스를 사용합니다. 

## MainActivity Class
>private GpsTracker gpsTracker

- gpsTracker객체를 담을 변수

>private static final int GPS_ENABLE_REQUEST_CODE = 2001;

- 나중에

>private static final int PERMISSIONS_REQUEST_CODE = 100;

- 나중에

>String[] REQUIRED_PERMISSIONS  = {Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION};

- 접근권한 배열에 담기

## onCreate()
> Description
- Permission체크 및 버튼 동작구현
> Parameter
- Bundle savedInstanceState 
- https://doraeul.tistory.com/43 
> Return
- type:void
- value:없음
> Dependence function
* if()
  - LocationService 활성화/비활성화에 따라 메소드실행
* onClick()
  - gpsTracker변수에 객체넣기
  - latitude,longitude변수에 위도,경도값을 넣기
  - address변수에 현재위치주소 넣기
> Code
 ```java
 protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);


        if (!checkLocationServicesStatus()) {

            showDialogForLocationServiceSetting();
        }else {

            checkRunTimePermission();
        }

        final TextView textview_address = (TextView)findViewById(R.id.textview);


        Button ShowLocationButton = (Button) findViewById(R.id.button);
        ShowLocationButton.setOnClickListener(new View.OnClickListener()
        {
            @Override
            public void onClick(View arg0)
            {

                gpsTracker = new GpsTracker(MainActivity.this);

                double latitude = gpsTracker.getLatitude();
                double longitude = gpsTracker.getLongitude();

                String address = getCurrentAddress(latitude, longitude);
                textview_address.setText(address);

                Toast.makeText(MainActivity.this, "현재위치 \n위도 " + latitude + "\n경도 " + longitude, Toast.LENGTH_LONG).show();
            }
        });
    }
```    
## onRequestPermissionsResult()
> Description
- requestPermissions를 사용한 퍼미션 요청의 결과를 리턴받는 메소드입니다
- android studio버전업했더니 빨간줄뜸 작동은잘됨
> Parameter
- int permsRequestCode 
- https://developer.android.com/training/permissions/requesting#java
> Return
- type:void
- value:없음
> Dependence function
* if(permsRequestCode == PERMISSIONS_REQUEST_CODE && grandResults.length == REQUIRED_PERMISSIONS.length)
  - 요청 코드가 PERMISSIONS_REQUEST_CODE 이고, 요청한 퍼미션 개수만큼 수신되었다면
  - for문으로 모든 퍼미션을 허용했는지 체크합니다.
* if(check_result)
  - 위치값을 가져올 수 있는지 체크
  - 없으면 앱을 사용할 수 없는 이유를 설명해주고 앱을 종료합니다.
> Code
```java
@Override
    public void onRequestPermissionsResult(int permsRequestCode,
                                           @NonNull String[] permissions,
                                           @NonNull int[] grandResults) {

        if ( permsRequestCode == PERMISSIONS_REQUEST_CODE && grandResults.length == REQUIRED_PERMISSIONS.length) {

            // 요청 코드가 PERMISSIONS_REQUEST_CODE 이고, 요청한 퍼미션 개수만큼 수신되었다면

            boolean check_result = true;


            // 모든 퍼미션을 허용했는지 체크합니다.

            for (int result : grandResults) {
                if (result != PackageManager.PERMISSION_GRANTED) {
                    check_result = false;
                    break;
                }
            }


            if ( check_result ) {

                //위치 값을 가져올 수 있음
                ;
            }
            else {
                // 거부한 퍼미션이 있다면 앱을 사용할 수 없는 이유를 설명해주고 앱을 종료합니다.2 가지 경우가 있습니다.

                if (ActivityCompat.shouldShowRequestPermissionRationale(this, REQUIRED_PERMISSIONS[0])
                        || ActivityCompat.shouldShowRequestPermissionRationale(this, REQUIRED_PERMISSIONS[1])) {

                    Toast.makeText(MainActivity.this, "퍼미션이 거부되었습니다. 앱을 다시 실행하여 퍼미션을 허용해주세요.", Toast.LENGTH_LONG).show();
                    finish();


                }else {

                    Toast.makeText(MainActivity.this, "퍼미션이 거부되었습니다. 설정(앱 정보)에서 퍼미션을 허용해야 합니다. ", Toast.LENGTH_LONG).show();

                }
            }

        }
    }
```
