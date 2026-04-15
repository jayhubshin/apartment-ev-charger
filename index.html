// 전역 변수
let map;
let markers = [];
let geocoder;
let currentApartments = [];
let filteredApartments = [];

const CORS_PROXY = 'https://api.allorigins.win/raw?url=';

// 초기화
document.addEventListener('DOMContentLoaded', function() {
    initializeKakaoMap();
    checkApiKeys();
    
    // 엔터키 검색 지원
    document.getElementById('searchInput').addEventListener('keypress', function(e) {
        if (e.key === 'Enter') {
            performSearch();
        }
    });
});

// 카카오맵 초기화
function initializeKakaoMap() {
    const container = document.getElementById('kakaoMap');
    const options = {
        center: new kakao.maps.LatLng(37.5665, 126.9780), // 서울시청
        level: 8
    };
    
    map = new kakao.maps.Map(container, options);
    geocoder = new kakao.maps.services.Geocoder();
    
    // 지도 컨트롤 추가
    const zoomControl = new kakao.maps.ZoomControl();
    map.addControl(zoomControl, kakao.maps.ControlPosition.RIGHT);
}

// API 키 확인
function checkApiKeys() {
    if (!API_KEYS.molit || !API_KEYS.environment) {
        showApiKeyModal();
    }
}

// 메인 검색 함수
async function performSearch() {
    const keyword = document.getElementById('searchInput').value.trim();
    const regionCode = document.getElementById('regionSelect').value;
    
    if (!keyword) {
        alert('검색어를 입력해주세요.');
        return;
    }
    
    showLoadingIndicator();
    
    try {
        // 1단계: 아파트 목록 조회
        const apartments = await searchApartments(keyword, regionCode);
        
        if (apartments.length === 0) {
            showNoResults();
            return;
        }
        
        // 2단계: 각 아파트의 충전기 정보 조회
        const enrichedApartments = await Promise.all(
            apartments.map(apt => enrichApartmentWithChargers(apt))
        );
        
        currentApartments = enrichedApartments;
        filteredApartments = [...enrichedApartments];
        
        displayResults(filteredApartments);
        plotMarkersOnMap(filteredApartments);
        
    } catch (error) {
        console.error('검색 오류:', error);
        showErrorMessage('검색 중 오류가 발생했습니다.');
    } finally {
        hideLoadingIndicator();
    }
}

// 아파트 데이터에 충전기 정보 추가
async function enrichApartmentWithChargers(apartment) {
    try {
        // 좌표 변환
        const coords = await getCoordinates(apartment.kaptAddr);
        if (coords) {
            apartment.lat = coords.lat;
            apartment.lng = coords.lng;
        }
        
        // 충전기 정보 조회
        const chargers = await getEVChargers(apartment.kaptName, apartment.kaptAddr);
        const matchedChargers = matchChargersToApartment(chargers, apartment);
        
        apartment.chargers = matchedChargers;
        apartment.stats = generateStatistics(apartment, matchedChargers);
        
        return apartment;
    } catch (error) {
        console.error(`아파트 ${apartment.kaptName} 데이터 처리 오류:`, error);
        apartment.chargers = [];
        apartment.stats = generateStatistics(apartment, []);
        return apartment;
    }
}

// 좌표 변환 함수
function getCoordinates(address) {
    return new Promise((resolve) => {
        geocoder.addressSearch(address, function(result, status) {
            if (status === kakao.maps.services.Status.OK) {
                resolve({
                    lat: parseFloat(result[0].y),
                    lng: parseFloat(result[0].x)
                });
            } else {
                resolve(null);
            }
        });
    });
}

// 결과 표시
function displayResults(apartments) {
    const resultsList = document.getElementById('resultsList');
    resultsList.innerHTML = '';
    
    if (apartments.length === 0) {
        resultsList.innerHTML = '<p class="no-results">검색 결과가 없습니다.</p>';
        return;
    }
    
    apartments.forEach((apartment, index) => {
        const card = createApartmentCard(apartment, index);
        resultsList.appendChild(card);
    });
}

// 아파트 카드 생성
function createApartmentCard(apartment, index) {
    const stats = apartment.stats;
    const hasChargers = stats.chargerCount > 0;
    
    const card = document.createElement('div');
    card.className = 'apartment-card';
    card.onclick = () => showApartmentDetail(apartment);
    
    card.innerHTML = `
        <h3>
            ${apartment.kaptName}
            <span class="ev-badge ${hasChargers ? 'has-chargers' : 'no-chargers'}">
                ${hasChargers ? `⚡ ${stats.chargerCount}기` : '❌ 없음'}
            </span>
        </h3>
        <div class="apartment-info">
            📍 ${apartment.kaptAddr || '주소 정보 없음'}
        </div>
        <div class="stats-grid">
            <div class="stat-item">
                <div class="stat-value">${stats.totalParking.toLocaleString()}</div>
                <div class="stat-label">총 주차면</div>
            </div>
            <div class="stat-item">
                <div class="stat-value">${stats.chargerCount}</div>
                <div class="stat-label">충전기 수</div>
            </div>
            <div class="stat-item">
                <div class="stat-value">${stats.ratio}%</div>
                <div class="stat-label">설치 비율</div>
            </div>
        </div>
        ${hasChargers ? `
        <div class="progress-bar">
            <div class="progress-fill" style="width: ${Math.min(stats.ratio * 5, 100)}%"></div>
        </div>
        ` : ''}
    `;
    
    return card;
}

// 지도에 마커 표시
function plotMarkersOnMap(apartments) {
    // 기존 마커 제거
    markers.forEach(marker => marker.setMap(null));
    markers = [];
    
    const bounds = new kakao.maps.LatLngBounds();
    
    apartments.forEach(apartment => {
        if (!apartment.lat || !apartment.lng) return;
        
        const position = new kakao.maps.LatLng(apartment.lat, apartment.lng);
        bounds.extend(position);
        
        const hasChargers = apartment.stats.chargerCount > 0;
        const markerImage = createMarkerImage(hasChargers);
        
        const marker = new kakao.maps.Marker({
            position: position,
            image: markerImage,
            title: apartment.kaptName
        });
        
        marker.setMap(map);
        markers.push(marker);
        
        // 인포윈도우
        const infoWindow = new kakao.maps.InfoWindow({
            content: `
                <div style="padding: 10px; min-width: 200px;">
                    <strong>${apartment.kaptName}</strong><br>
                    충전기: ${apartment.stats.chargerCount}기<br>
                    설치비율: ${apartment.stats.ratio}%
                </div>
            `
        });
        
        kakao.maps.event.addListener(marker, 'click', function() {
            infoWindow.open(map, marker);
            showApartmentDetail(apartment);
        });
    });
    
    // 모든 마커가 보이도록 지도 범위 조정
    if (apartments.length > 0) {
        map.setBounds(bounds);
    }
}

// 마커 이미지 생성
function createMarkerImage(hasChargers) {
    const imageSrc = hasChargers 
        ? 'https://t1.daumcdn.net/localimg/localimages/07/mapapidoc/markerStar.png'
        : 'https://t1.daumcdn.net/localimg/localimages/07/mapapidoc/marker_red.png';
    
    const imageSize = new kakao.maps.Size(24, 35);
    return new kakao.maps.MarkerImage(imageSrc, imageSize);
}

// 아파트 상세 정보 표시
function showApartmentDetail(apartment) {
    const modal = document.getElementById('detailModal');
    const content = document.getElementById('detailContent');
    
    const stats = apartment.stats;
    const chargers = apartment.chargers;
    
    content.innerHTML = `
        <h2>${apartment.kaptName}</h2>
        <p><strong>주소:</strong> ${apartment.kaptAddr}</p>
        <p><strong>시공사:</strong> ${apartment.kaptBcompany || '정보 없음'}</p>
        
        <div class="stats-summary">
            <h3>📊 설치 현황 요약</h3>
            <div class="stats-grid">
                <div class="stat-item">
                    <div class="stat-value">${stats.totalParking.toLocaleString()}</div>
                    <div class="stat-label">총 주차면</div>
                </div>
                <div class="stat-item">
                    <div class="stat-value">${stats.chargerCount}</div>
                    <div class="stat-label">충전기 수</div>
                </div>
                <div class="stat-item">
                    <div class="stat-value">${stats.ratio}%</div>
                    <div class="stat-label">설치 비율</div>
                </div>
            </div>
        </div>
        
        ${stats.chargerCount > 0 ? `
        <div class="location-stats">
            <h3>📍 설치 위치별 현황</h3>
            <p>지하: ${stats.locationStats.지하}기, 지상: ${stats.locationStats.지상}기, 위치미상: ${stats.locationStats.위치미상}기</p>
        </div>
        
        <div class="operator-stats">
            <h3>🏢 운영사별 현황</h3>
            ${Object.entries(stats.operatorStats).map(([operator, count]) => 
                `<p>${operator}: ${count}기</p>`
            ).join('')}
        </div>
        
        <div class="charger-details">
            <h3>🔌 충전기 상세 목록</h3>
            <table class="charger-table">
                <thead>
                    <tr>
                        <th>운영사</th>
                        <th>용량</th>
                        <th>타입</th>
                        <th>위치</th>
                        <th>설치년도</th>
                    </tr>
                </thead>
                <tbody>
                    ${chargers.map(charger => `
                        <tr>
                            <td>${charger.busiNm || '미확인'}</td>
                            <td>${charger.output || '-'}</td>
                            <td>${charger.chgerType || '-'}</td>
                            <td>${parseLocation(charger.note || charger.location)}</td>
                            <td>${charger.installDate?.substring(0, 4) || '-'}</td>
                        </tr>
                    `).join('')}
                </tbody>
            </table>
        </div>
        ` : `
        <div class="no-chargers">
            <h3>❌ 전기차 충전기 없음</h3>
            <p>이 아파트 단지에는 현재 설치된 전기차 충전기가 없습니다.</p>
        </div>
        `}
    `;
    
    modal.classList.remove('hidden');
}

// 모달 닫기
function closeModal() {
    document.getElementById('detailModal').classList.add('hidden');
}

// 정렬 적용
function applySorting() {
    const sortBy = document.getElementById('sortBy').value;
    
    filteredApartments.sort((a, b) => {
        switch (sortBy) {
            case 'name':
                return a.kaptName.localeCompare(b.kaptName);
            case 'ratio_desc':
                return b.stats.ratio - a.stats.ratio;
            case 'count_desc':
                return b.stats.chargerCount - a.stats.chargerCount;
            default:
                return 0;
        }
    });
    
    displayResults(filteredApartments);
}

// 필터 적용
function applyFiltering() {
    const filterEV = document.getElementById('filterEV').value;
    
    switch (filterEV) {
        case 'has_ev':
            filteredApartments = currentApartments.filter(apt => apt.stats.chargerCount > 0);
            break;
        case 'no_ev':
            filteredApartments = currentApartments.filter(apt => apt.stats.chargerCount === 0);
            break;
        default:
            filteredApartments = [...currentApartments];
    }
    
    displayResults(filteredApartments);
    plotMarkersOnMap(filteredApartments);
}

// 유틸리티 함수들
function showLoadingIndicator() {
    // 로딩 표시 구현
}

function hideLoadingIndicator() {
    // 로딩 숨김 구현
}

function showNoResults() {
    document.getElementById('resultsList').innerHTML = '<p class="no-results">검색 결과가 없습니다.</p>';
}

function showErrorMessage(message) {
    alert(message);
}

function showApiKeyModal() {
    // API 키 입력 모달 표시
}
