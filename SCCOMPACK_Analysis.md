# SCCOMPACK 소스 코드 분석 보고서

## 1. 개요 (Overview)
**SCCOMPACK**은 의료 정보 교류(HIE, Health Information Exchange) 또는 외부 의료 서비스(Xave Service)와의 연동을 담당하는 델파이(Delphi) 패키지로 분석됩니다.
주로 SOAP/Web Service 프로토콜을 사용하여 외부 시스템과 의료 데이터(환자 정보, 진료 의뢰, 영상 정보 등)를 송수신하는 기능을 포함하고 있습니다.

## 2. 디렉토리 구조 및 파일 구성
- **핵심 파일**:
  - `SCXaveUtils.pas`: 외부 서비스 연동을 위한 메인 유틸리티/래퍼(Wrapper) 유닛.
  - `XaveService1.pas`: WSDL에서 생성된 SOAP 클라이언트 인터페이스 및 데이터 모델(DTO) 정의.
  - `ModelService.pas`: 내부 데이터 모델 서비스로 추정.
- **보조 라이브러리**:
  - `SuperObject.pas`, `superxmlparser.pas`, `supertimezone.pas`: JSON 및 XML 데이터 처리를 위한 오픈 소스 라이브러리.
  - `CDA_StyleSheet_*.xsl`: CDA(Clinical Document Architecture) 문서를 보여주기 위한 스타일시트 파일들.

## 3. 주요 모듈 분석

### 3.1. XaveService1.pas (SOAP Interface Layer)
- **역할**: `XaveService.xml` (WSDL)을 기반으로 자동 생성된 파일로 보입니다.
- **주요 구성**:
  - 외부 서비스와 통신하기 위한 입출력 객체(Class)들이 정의되어 있습니다.
  - 예: `GetMPIIdRequestObject`, `MedicalHistoryRequestModel`, `Notification_INOUT` 등.
  - 외부 시스템과 데이터를 주고받는 **규격(Protocol)**을 정의합니다.

### 3.2. SCXaveUtils.pas (Service Adapter Layer)
- **역할**: `XaveService1` 유닛을 사용하여 실제 비즈니스 로직을 수행하는 함수들을 제공합니다. 클라이언트 프로그램이 이 유닛의 함수를 호출하여 외부 통신을 수행합니다.
- **주요 기능 (Functions)**:
  - **환자 식별**: `GetMpiId` (환자식별번호 조회), `GetSSN` (주민번호 조회).
  - **진료 의뢰/회송**: `CancelDocument` (문서 취소), `AddReferralStatus` (교류 상태 등록), `AddSpecialReferralEncounter` (진료상태 등록).
  - **정보 조회**: `GetMedicalStaff` (의료진 조회), `GetRepository` (저장소 조회), `GetStrongPointList` (거점 의료기관 조회).
  - **영상(PACS) 연동**: `GetPACSViewerToken`, `AddUploadRadiologyInfo` (영상 업로드 정보 추가).
  - **기타**: `GetCommonCode` (공통코드 조회), `GetKostomTerminology` (보건의료용어 조회).

### 3.3. 의존성 (Dependencies)
- **Delphi Standard**: `Soap.InvokeRegistry`, `Data.DB`, `Winapi.Windows`.
- **Indy Components**: `IdHTTP`, `IdSSLOpenSSL` (HTTPS 통신 지원).
- **JSON/XML**: `SuperObject` (JSON 파싱).

## 4. 결론
SCCOMPACK은 병원 정보 시스템(HIS) 내에서 **진료 의뢰-회송 시범사업** 또는 **의료 영상 정보 교류**와 같은 **외부 의료기관 간의 데이터 연동**을 처리하는 핵심 모듈입니다. SOAP 기반의 웹 서비스를 호출하여 환자 정보를 식별하고, 진료 기록(CDA) 및 영상 데이터를 안전하게 전송하는 역할을 수행합니다.
