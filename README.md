# -*- coding: utf-8 -*-

import json

def build_response(status_code, body):
    """统一返回格式"""
    return {
        'statusCode': status_code,
        'headers': {
            'Content-Type': 'application/json',
            'Access-Control-Allow-Origin': '*'
        },
        'body': json.dumps(body, ensure_ascii=False)
    }

def main(event, context):
    """云函数入口 - 函数名改为 main 以匹配执行方法"""
    print("收到事件:", json.dumps(event, ensure_ascii=False))
    
    # 解析参数
    params = {}
    if event.get('queryStringParameters'):
        params.update(event['queryStringParameters'])
    if event.get('body'):
        try:
            body_str = event['body']
            if event.get('isBase64Encoded'):
                import base64
                body_str = base64.b64decode(body_str).decode('utf-8')
            params.update(json.loads(body_str))
        except:
            pass
    
    action = params.get('action', 'list')
    keyword = params.get('keyword', '').strip()
    
    # 测试数据
    test_funds = [
        {'fundCode': '000041', 'fundName': '华夏全球精选QDII', 'fundType': 'QDII', 'purchaseStatus': '限额', 'dailyLimit': 10000},
        {'fundCode': '002230', 'fundName': '华夏大中华QDII', 'fundType': 'QDII', 'purchaseStatus': '限额', 'dailyLimit': 5000},
        {'fundCode': '486002', 'fundName': '工银全球精选QDII', 'fundType': 'QDII', 'purchaseStatus': '开放申购', 'dailyLimit': 0}
    ]
    
    # 搜索功能
    if action == 'search' and keyword:
        keyword_lower = keyword.lower()
        filtered = [f for f in test_funds if keyword_lower in f['fundCode'].lower() or keyword_lower in f['fundName'].lower()]
        return build_response(200, {
            'code': 0,
            'message': 'success（测试模式）',
            'data': {
                'total': len(filtered),
                'keyword': keyword,
                'list': filtered,
                'note': '演示数据，安装 akshare 后可获取真实数据'
            }
        })
    
    return build_response(200, {
        'code': 0,
        'message': 'success（测试模式）',
        'data': {
            'total': len(test_funds),
            'list': test_funds,
            'note': '演示数据，安装 akshare 后可获取真实数据'
        }
    })
