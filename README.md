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
  - 전달된 요청 코드
- @NonNull String[] permissions
  - 요청 된 권한
- @NonNull int[] grandResults
  - 해당 권한에 대한 부여 결과
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
## checkRunTimePermission()
> Description
- 런타임 퍼미션 처리
> Parameter
- 없음
> Return
- type:void
- value:없음
> Dependence function
* if (hasFineLocationPermission == PackageManager.PERMISSION_GRANTED &&
                hasCoarseLocationPermission == PackageManager.PERMISSION_GRANTED)
  - 위치 퍼미션을 가지고 있는지 체크합니다.
  - 퍼미션 요청을 허용한 적이 없다면 퍼미션 요청이 필요합니다.사용자가 퍼미션 거부를 한 적이 있는 경우에는 사용자게에 퍼미션 요청을 합니다. 요청 결과는 onRequestPermissionResult에서 수신됩니다. 사용자가 퍼미션 거부를 한 적이 없는 경우에는 퍼미션 요청을 바로 합니다. 요청 결과는 onRequestPermissionResult에서 수신됩니다.

> Code
```java
void checkRunTimePermission(){

        //런타임 퍼미션 처리
        // 1. 위치 퍼미션을 가지고 있는지 체크합니다.
        int hasFineLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
                Manifest.permission.ACCESS_FINE_LOCATION);
        int hasCoarseLocationPermission = ContextCompat.checkSelfPermission(MainActivity.this,
                Manifest.permission.ACCESS_COARSE_LOCATION);


        if (hasFineLocationPermission == PackageManager.PERMISSION_GRANTED &&
                hasCoarseLocationPermission == PackageManager.PERMISSION_GRANTED) {

            // 2. 이미 퍼미션을 가지고 있다면
            // ( 안드로이드 6.0 이하 버전은 런타임 퍼미션이 필요없기 때문에 이미 허용된 걸로 인식합니다.)


            // 3.  위치 값을 가져올 수 있음



        } else {  //2. 퍼미션 요청을 허용한 적이 없다면 퍼미션 요청이 필요합니다. 2가지 경우(3-1, 4-1)가 있습니다.

            // 3-1. 사용자가 퍼미션 거부를 한 적이 있는 경우에는
            if (ActivityCompat.shouldShowRequestPermissionRationale(MainActivity.this, REQUIRED_PERMISSIONS[0])) {

                // 3-2. 요청을 진행하기 전에 사용자가에게 퍼미션이 필요한 이유를 설명해줄 필요가 있습니다.
                Toast.makeText(MainActivity.this, "이 앱을 실행하려면 위치 접근 권한이 필요합니다.", Toast.LENGTH_LONG).show();
                // 3-3. 사용자게에 퍼미션 요청을 합니다. 요청 결과는 onRequestPermissionResult에서 수신됩니다.
                ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                        PERMISSIONS_REQUEST_CODE);


            } else {
                // 4-1. 사용자가 퍼미션 거부를 한 적이 없는 경우에는 퍼미션 요청을 바로 합니다.
                // 요청 결과는 onRequestPermissionResult에서 수신됩니다.
                ActivityCompat.requestPermissions(MainActivity.this, REQUIRED_PERMISSIONS,
                        PERMISSIONS_REQUEST_CODE);
            }

        }

    }
```
## getCurrentAddress()

> Description
- 지오코더 Gps를 주소로 변환
> Parameter
- double latitude, double longitude
- 위도 경도
> Return
- type:String
- value:지오코더 주소반환
> Dependence function
* try catch로 에러처리
* geocoder https://developer.android.com/reference/android/location/Geocoder
* List<Address> addresses 
  - 이름이 지정된 위치를 설명하려고 시도하는 주소 배열을 반환함
> Code

```java
 public String getCurrentAddress( double latitude, double longitude) {

        //지오코더... GPS를 주소로 변환
        Geocoder geocoder = new Geocoder(this, Locale.getDefault());

        List<Address> addresses;

        try {

            addresses = geocoder.getFromLocation(
                    latitude,
                    longitude,
                    7);
        } catch (IOException ioException) {
            //네트워크 문제
            Toast.makeText(this, "지오코더 서비스 사용불가", Toast.LENGTH_LONG).show();
            return "지오코더 서비스 사용불가";
        } catch (IllegalArgumentException illegalArgumentException) {
            Toast.makeText(this, "잘못된 GPS 좌표", Toast.LENGTH_LONG).show();
            return "잘못된 GPS 좌표";

        }



        if (addresses == null || addresses.size() == 0) {
            Toast.makeText(this, "주소 미발견", Toast.LENGTH_LONG).show();
            return "주소 미발견";

        }

        Address address = addresses.get(0);
        return address.getAddressLine(0).toString()+"\n";

    }
```
  
##showDialogForLocationServiceSetting()
  
> Description
- 대화상자 세팅
> Parameter
- 없음
> Return
- type: void
- value:없음
> Dependence function
* https://developer.android.com/reference/android/app/AlertDialog.Builder
> Code
  ```java
    private void showDialogForLocationServiceSetting() {

        AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
        builder.setTitle("위치 서비스 비활성화");
        builder.setMessage("앱을 사용하기 위해서는 위치 서비스가 필요합니다.\n"
                + "위치 설정을 수정하실래요?");
        builder.setCancelable(true);
        builder.setPositiveButton("설정", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int id) {
                Intent callGPSSettingIntent
                        = new Intent(android.provider.Settings.ACTION_LOCATION_SOURCE_SETTINGS);
                startActivityForResult(callGPSSettingIntent, GPS_ENABLE_REQUEST_CODE);
            }
        });
        builder.setNegativeButton("취소", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int id) {
                dialog.cancel();
            }
        });
        builder.create().show();
    }
  ```

## onActivityResult()
> Description
  - startActivityForResult메소드의 콜백 메소드
  - https://developer.android.com/reference/android/app/Activity?hl=ko#onActivityResult(int,%20int,%20android.content.Intent)
> Parameter
- requestCode
  - 대화상자 액티비티랑 MainActivity를 구별하기 위한 코드  
- resultCode
  - 어떠한 결과코드를 주었는지에 대한 변수
- data
  - 액티비티에서 보낸 결과 데이터가 들어가있는 부분
> Return
- type: void
- value:없음
> Dependence function
* https://developer.android.com/reference/android/app/Activity#onActivityResult(int,%20int,%20android.content.Intent)
> Code
  
```java
     protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        switch (requestCode) {

            case GPS_ENABLE_REQUEST_CODE:

                //사용자가 GPS 활성 시켰는지 검사
                if (checkLocationServicesStatus()) {
                    if (checkLocationServicesStatus()) {

                        Log.d("@@@", "onActivityResult : GPS 활성화 되있음");
                        checkRunTimePermission();
                        return;
                    }
                }

                break;
        }
    }
```
## checkLocationServicesStatus()
> Description
- LocationManager 의 GPS_PRIVIDER 또는 NETWORK_PROVIDER 가 동작 중인지 확인하여 결과 값을 리턴한다.
- LocationManager 개체 getSystemSerivce()를 이용하여 얻는다.
> Parameter
  - 없음
> Return
- type: boolean
- value: GPS_PRIVIDER 또는 NETWORK_PROVIDER 가 Enable 이면 True, 아니면 False 값.
> Dependence function
* getSystemService()
  - 주어진 파라미터에 대응되는 안드로이드가 제공하는 시스템 서비스를 요청한다.
  - https://developer.android.com/reference/android/content/Context#getSystemService(java.lang.String)
* locationManager.isProviderEnabled()
  - 지정된 Provider의 Enabled/Disabled 상태를 Return합니다.
  - https://developer.android.com/reference/android/location/LocationManager#isProviderEnabled(java.lang.String)
> Code  
  ```java
  public boolean checkLocationServicesStatus() {
        LocationManager locationManager = (LocationManager) getSystemService(LOCATION_SERVICE);

        return locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER)
                || locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER);
    }

}
  ```
# GpsTracker Class
> Description
  - GpsTracker.java는 현재 위치를 가져와 주소로 변환하는 처리를 합니다. 

> final Context mContext;
  - 액티비티 정보를 얻어오는 변수
> Location location;
  - 위치
> double latitude;
  - 위도
 > double longitude;
   - 경도
> MIN_DISTANCE_CHANGE_FOR_UPDATES
  -  gps정보 업데이트 거리
> MIN_TIME_BW_UPDATES
  - gps정보 업데이트 시간
> locationManager
  - 위치관리자서비스 객체를 담을 변수

## GpsTracker()
> Description
  - getLocation메소드 호출
  - mContext변수 초기화
> Parameter
- context
  - 액티비티 정보를 얻어오는 변수
  - https://developer.android.com/reference/android/content/Context
  - https://zxcv5500.tistory.com/258
> Return
- type: void
- value:없음
> Dependence function
* getlocation()
> Code
  ```java
      public GpsTracker(Context context) {
        this.mContext = context;
        getLocation();
    }
  ```
## getLocation()
> Description
  - 위치를 리턴하는 메소드
> Parameter
  - 없음
> Return
- type: Location
- value:위치
> Dependence function
  > getSystemService()
    - 주어진 파라미터에 대응되는 안드로이드가 제공하는 시스템 서비스를 요청한다.
    - https://developer.android.com/reference/android/content/Context#getSystemService(java.lang.String)
  > locationManager.isProviderEnabled()
    - 지정된 Provider의 Enabled/Disabled 상태를 Return합니다.
    - https://developer.android.com/reference/android/location/LocationManager#isProviderEnabled(java.lang.String)
> Code
  ```java
  public Location getLocation() {
        try {
            locationManager = (LocationManager) mContext.getSystemService(LOCATION_SERVICE);

            boolean isGPSEnabled = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);
            boolean isNetworkEnabled = locationManager.isProviderEnabled(LocationManager.NETWORK_PROVIDER);

            if (!isGPSEnabled && !isNetworkEnabled) {

            } else {

                int hasFineLocationPermission = ContextCompat.checkSelfPermission(mContext,
                        Manifest.permission.ACCESS_FINE_LOCATION);
                int hasCoarseLocationPermission = ContextCompat.checkSelfPermission(mContext,
                        Manifest.permission.ACCESS_COARSE_LOCATION);


                if (hasFineLocationPermission == PackageManager.PERMISSION_GRANTED &&
                        hasCoarseLocationPermission == PackageManager.PERMISSION_GRANTED) {

                    ;
                } else
                    return null;


                if (isNetworkEnabled) {


                    locationManager.requestLocationUpdates(LocationManager.NETWORK_PROVIDER, MIN_TIME_BW_UPDATES, MIN_DISTANCE_CHANGE_FOR_UPDATES, this);

                    if (locationManager != null)
                    {
                        location = locationManager.getLastKnownLocation(LocationManager.NETWORK_PROVIDER);
                        if (location != null)
                        {
                            latitude = location.getLatitude();
                            longitude = location.getLongitude();
                        }
                    }
                }


                if (isGPSEnabled)
                {
                    if (location == null)
                    {
                        locationManager.requestLocationUpdates(LocationManager.GPS_PROVIDER, MIN_TIME_BW_UPDATES, MIN_DISTANCE_CHANGE_FOR_UPDATES, this);
                        if (locationManager != null)
                        {
                            location = locationManager.getLastKnownLocation(LocationManager.GPS_PROVIDER);
                            if (location != null)
                            {
                                latitude = location.getLatitude();
                                longitude = location.getLongitude();
                            }
                        }
                    }
                }
            }
        }
        catch (Exception e)
        {
            Log.d("@@@", ""+e.toString());
        }

        return location;
    }
  ```
## getLatitude()
  
> Description
  - 위도 변수 초기화
  - MainActivity에서 불러올 함수
> Parameter
  - 없음
> Return
- type: double
- value:위도 변수 리턴
> Dependence function  
  - location.getLatitude()
  - 기기의 위도를 리턴
> Code
  ```java
   public double getLatitude()
    {
        if(location != null)
        {
            latitude = location.getLatitude();
        }

        return latitude;
    }
  ```
## getLongitude()
> Description
  - 경도 변수 초기화
  - MainActivity에서 불러올 함수
> Parameter
  - 없음
> Return
- type: double
- value:경도  변수 리턴
> Dependence function  
  - location.getLatitude()
  - 기기의 경도를 리턴
> Code
  ```java
     public double getLongitude()
    {
        if(location != null)
        {
            longitude = location.getLongitude();
        }

        return longitude;
    }
  ```
  
