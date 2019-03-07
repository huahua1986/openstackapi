import json
import urllib2
def byteify(input):
    if isinstance(input, dict):
        return {byteify(key): byteify(value) for key, value in input.iteritems()}
    elif isinstance(input, list):
        return [byteify(element) for element in input]
    elif isinstance(input, unicode):
        return input.encode('utf-8')
    else:
        return input

def token_post(auth,auth_url):
    headers = {"Content-type":"application/json","Accept": "application/json;charset=UTF-8"}
    token_url = auth_url+"auth/tokens"
    req = urllib2.Request(token_url, auth, headers)
    response = urllib2.urlopen(req)
    token = response.info().getheader("X-Subject-Token")
    response.encoding='utf-8'
    response_json = json.loads(response.read())
    response_json = byteify(response_json)
    response_json['authtoken'] = token
    return response_json

def get_nova_endpoint(response):
    services = response['token']['catalog']
    for i in range(0, len(services)):
        if services[i]['name'] == 'nova':
            nova_endpoint = services[i]['endpoints'][0]['url']
    return nova_endpoint

def get_image_id(novaurl,tokenid,imagename):
    servers_url = novaurl+'/'+'images'
    req = urllib2.Request(servers_url)
    req.add_header('X-Auth-Token', tokenid)
    response = urllib2.urlopen(req)
    services = json.loads(response.read())['images']
    for i in range(0, len(services)):
        if services[i]['name'] == imagename:
            image_id = services[i]['id']
    return image_id

def get_flavor_id(novaurl,tokenid,flavorname):
    servers_url = novaurl+'/'+'flavors'
    req = urllib2.Request(servers_url)
    req.add_header('X-Auth-Token', tokenid)
    response = urllib2.urlopen(req)
    services = json.loads(response.read())['flavors']
    for i in range(0, len(services)):
        if services[i]['name'] == flavorname:
            flavor_id = services[i]['id']
    return flavor_id

def get_network_id(novaurl,tokenid,networkname):
    servers_url = novaurl+'/'+'os-networks'
    req = urllib2.Request(servers_url)
    req.add_header('X-Auth-Token', tokenid)
    response = urllib2.urlopen(req)
    services = json.loads(response.read())['networks']
    for i in range(0, len(services)):
        if services[i]['label'] == networkname:
            network_id = services[i]['id']
    return network_id

def create_vmserver(novaurl,tokenid,vmname,imange_id,flaver_id,network_id):
    headers = {"Content-type":"application/json","Accept": "application/json;charset=UTF-8","X-Auth-Token":tokenid}
    servers_url = novaurl+"/servers"
    body={"server": {"name": vmname, "imageRef": imange_id, "flavorRef": flaver_id, "networks": [{"uuid": network_id}]}}
    body=json.dumps(body)
    req = urllib2.Request(servers_url,body,headers) 
    response = urllib2.urlopen(req)
    response_json = json.loads(response.read())
    return response_json    

if __name__ == '__main__':
    auth = {"auth":{"identity":{"methods":["password"],"password":{"user":{"domain":{"id":"default"},"name":"admin","password":"admin"}}},"scope":{"project":{"domain":{"id":"default"},"name":"admin"}}}}
    auth = json.dumps(auth)
    auth_url = 'http://192.168.0.100:35357/v3/'
    response = token_post(auth,auth_url)
    tokenid = response['authtoken']
    novaurl = get_nova_endpoint(response)
    imange_id=get_image_id(novaurl,tokenid,'centos+')
    flaver_id=get_flavor_id(novaurl,tokenid,'1-2-20')
    network_id=get_network_id(novaurl,tokenid,'network-test')
    vmname=raw_input("plese input vm name:")
    create_vmserver(novaurl,tokenid,vmname,imange_id,flaver_id,network_id)
