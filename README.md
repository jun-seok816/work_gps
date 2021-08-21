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
