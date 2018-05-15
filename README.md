# dynamicAddForm
동적으로 필드그룹을 추가하는 스크립트
//동적으로 폼을 추가하는 곳
$(document).ready(function(){
    //group add limit
    var maxGroup = 10;
    var groupNum = 1;
    //add more fields group
    $("#CREATE_POS_GROUP").click(function(){		
        if($('body').find('.pos-table').length < maxGroup){
            var fieldHTML = '<div class="fieldGroup" id="fg_'+groupNum+'">'+$(".fieldGroupCopy").html()+'</div>';
            $('body').find('.fieldGroup:last').after(fieldHTML);			
			makeSerial();
        }else{
            alert('폼은 최대 '+maxGroup+' 까지만 가능합니다.');
        }
		groupNum++;
    });
    
    //remove fields group
    $("body").on("click",".DEL_GROUP_FORM",function(){
		var fg = $(this).parents("tbody"); 
		var pos_serial = fg.find('.pos-serial').val(); 
		if(confirm('정말로 삭제하시겠습니까?\n\n한번 삭제한 내용은 복구할 수 없습니다.')){
			$.ajax({
				type : 'POST',
				url : './store_update.php',
				data : 'w=rm&ps='+pos_serial+'&ogid=<?php echo $ogid; ?>',
				success: function(res){
					console.log(res);
					if(res == 1){
						
					} else {
						alert('데이터 삭제시 오류가 발생했습니다.');
					}
				}
			});
		}
        $(this).parents(".fieldGroup").remove();
    });
	
	$("body").on("click",".SAVE_GROUP_FORM",function(){ 
		var fg = $(this).parents("tbody");
		var pos_name = fg.find('.pos-name').val();		
		var pos_serial = fg.find('.pos-serial').val(); 
		var pos_gubun = fg.find('.pos-gubun option:selected').val();
		if(pos_name == ''){
			fg.find('.pos-name').focus();
			alert('POS명을 입력해주세요.');
			return false;
		}
		if(pos_gubun == ''){
			fg.find('.pos-gubun').focus();
			alert('POS를 선택해주세요.');	
			return false;				
		}
		
		$.ajax({
			type : 'POST',
			url : './store_update.php',
			data : 'w=pa&pn='+pos_name+'&ps='+pos_serial+'&pg='+pos_gubun+'&ogid='+<?php echo $ogid; ?>,
			success: function(res){
				if(res == 1){
					alert('POS가 등록되었습니다.');
				} else {
					alert('POS등록시 오류가 발생했습니다.');
				}
			}
		});

    });
});

function makeSerial()
{
	console.log('s');
	$.ajax({
		type : 'POST',
		url : './store_update.php',
		data : 'w=ms',
		success: function(res){
			if(res){				
				$('.fieldGroup:last').find('.pos-serial').val(res);
			}
		}
	});
}
