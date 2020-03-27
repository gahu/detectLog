<template>
    <div>
        <h1>{{msg}}</h1>
        <div id="map" style="width:100%; height:400px;"></div>
        <button class="button is-primary" @click="setMarker()">Input Map</button>
        <button @click="clearMarkers()">Clear Marker</button>
    </div>
</template>

<script>
// kakao 네임스페이스를 사용하기 위해서 위 주석을 사용해야 한다.
// 아니면 eslint 옵션을 수정

import bus_positions from '@/store/busstop.json';

export default {
    props: {

    },
    data() {
        return {
            msg: "카카오 맵 테스트",
            map: '',
            lats: 0.0,
            lngs: 0.0
        }
    },
    mounted() {
        window.kakao && window.kakao.maps ? this.initMap() : this.addScript();
    },
    methods : {
        initMap() {
            var container = document.getElementById('map');
            var options = {
                center: new kakao.maps.LatLng(37.2742, 127.0880),
                level: 6
            };
            // 지도를 생성
            this.map = new kakao.maps.Map(container, options);

            // 일반 지도와 스카이뷰로 지도 타입을 전환할 수 있는 지도타입 컨트롤을 생성
            var mapTypeControl = new kakao.maps.MapTypeControl();

            // 지도에 컨트롤을 추가해야 지도위에 표시
            // kakao.maps.ControlPosition은 컨트롤이 표시될 위치를 정의하는데 TOPRIGHT는 오른쪽 위를 의미
            this.map.addControl(mapTypeControl, kakao.maps.ControlPosition.TOPRIGHT);

            // 지도 확대 축소
            var zoomControl = new kakao.maps.ZoomControl();
            this.map.addControl(zoomControl, kakao.maps.ControlPosition.RIGHT);

            var positions = bus_positions.positions;

            // 마커 이미지의 주소
            var imageSrc = "http://t1.daumcdn.net/localimg/localimages/07/mapapidoc/markerStar.png";
            // var imageSrc = "/img/map-marker-black.png";

            var linePaths = [];
            var init_lats = 0.0;
            var init_lngs = 0.0;

            for (var i = 0; i < positions.length; i++) {
                // 마커 이미지의 이미지 크기
                var imageSize = new kakao.maps.Size(24, 35);

                // 마커 이미지를 생성
                var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize);

                positions[i].latlng = new kakao.maps.LatLng(positions[i].lat, positions[i].lng);

                init_lats += positions[i].lat;
                init_lngs += positions[i].lng;

                // 마커를 생성
                var marker = new kakao.maps.Marker({
                    map: this.map,   // 마커를 표시할 지도
                    position: positions[i].latlng,  // 마커를 표시할 위치
                    title: positions[i].title,   // 마커의 타이틀
                    image: markerImage  // 마커 이미지
                });

                linePaths.push(positions[i].latlng);
                
                marker.setMap(this.map);
            }

            var polyline = new kakao.maps.Polyline({
                path: linePaths,        // 선을 구성하는 좌표배열
                strokeweight: 5,        // 선의 두께
                strokeColor: '#FFAE00', // 선의 색상
                strokeOpacity: 0.7,     // 선의 불투명도
                strokeStyle: 'solid'    // 선의 스타일
            })

            this.lats = init_lats / positions.length;
            this.lngs = init_lngs / positions.length;

            this.map.setCenter(new kakao.maps.LatLng(this.lats, this.lngs));

            polyline.setMap(this.map);
        },

        addScript() {
            const script = document.createElement('script');
            /* global kakao */
            script.onload = () => kakao.maps.load(this.initMap);
            script.src = 'http://dapi.kakao.com/v2/maps/sdk.js?autoload=false&appkey=938089d746f95b6de32b253b7dae17be';
            document.head.appendChild(script);
        },

        createMarker(position, image) {
            var marker = new kakao.maps.Marker({
                position: position,
                image: image
            });

            return marker;
        },

        createMarkerImage(markerSize, offset, spriteOrigin) {
            var markerImage = new kakao.maps.MarkerImage(
                this.SPRITE_MARKER_URL,
                markerSize,
                {
                    offset: offset,
                    spriteOrigin: spriteOrigin,
                    spriteSize: this.spriteImageSize
                }
            );

            return markerImage;
        },

        clearMarkers () {
            var container = document.getElementById('map');
            var options = {
                center: new kakao.maps.LatLng(37.2742, 127.0880),
                level: 6
            };
            // 지도를 생성
            this.map = new kakao.maps.Map(container, options);
        },

        setCenter ({ lat, lng, maxLevel = 8 }) {
            if (this.map.getLevel() > maxLevel) {
                this.map.setLevel(maxLevel)
            }
            this.map.panTo(
                new kakao.maps.LatLng(lat, lng)
            )
        },

        setMarker() {
            var selected = this.$store.state.pickedTang;
            var tmpJson = this.$store.state.jsonGPSInfo[selected];
            var imageSrc = "/img/map-marker-black.png";
            var linePaths = [];

            var set_lats = 0.0;
            var set_lngs = 0.0;
            var cen_len = 0;

            for (var i = 0; i < tmpJson.length; i++) {
                // 마커 이미지의 이미지 크기
                var imageSize = new kakao.maps.Size(24, 35);

                // 마커 이미지를 생성
                var markerImage = new kakao.maps.MarkerImage(imageSrc, imageSize);

                if(tmpJson[i].lat != 0 && tmpJson[i].lng != 0 && !isNaN(tmpJson[i].lat) && !isNaN(tmpJson[i].lng)
                                        && typeof tmpJson[i].lat != "undefined" && typeof tmpJson[i].lng != "undefined") {
                    var latlng = new kakao.maps.LatLng(tmpJson[i].lat, tmpJson[i].lng);
                } else continue;

                set_lats += tmpJson[i].lat;
                set_lngs += tmpJson[i].lng;
                cen_len++;

                // 마커를 생성
                var marker = new kakao.maps.Marker({
                    map: this.map,   // 마커를 표시할 지도
                    position: latlng,  // 마커를 표시할 위치
                    content: i,
                    image: markerImage  // 마커 이미지
                });

                linePaths.push(latlng);
                
                marker.setMap(this.map);
            }

            var polyline = new kakao.maps.Polyline({
                path: linePaths,        // 선을 구성하는 좌표배열
                strokeweight: 5,        // 선의 두께
                strokeColor: '#013ADF', // 선의 색상
                strokeOpacity: 0.7,     // 선의 불투명도
                strokeStyle: 'solid'    // 선의 스타일
            })

            this.lats = set_lats / cen_len;
            this.lngs = set_lngs / cen_len;

            this.map.setCenter(new kakao.maps.LatLng(this.lats, this.lngs));

            polyline.setMap(this.map);
        }
        
    }
}
</script>

<style lang="scss" scoped>

</style>