#!/usr/bin/python2
import os,sys,base64,yaml,pprint,colorama,requests,math,time,subprocess,tqdm


ga_num='';reponame='';

pr=['GITHUB_REPOSITORY','TRAVIS_REPO_SLUG','SEMAPHORE_PROJECT_NAME','BUILD_REPOSITORY_NAME','BUDDY_SCM_URL','CF_REPO_NAME','CI_REPO_NAME','CIRRUS_REPO_NAME','REPO_FULL_NAME','CIRCLE_PROJECT_REPONAME','CI_PROJECT_NAME','SCRUTINIZER_PROJECT','APPVEYOR_PROJECT_SLUG']

for prr in pr:  
  if os.environ.has_key(prr) and os.environ[prr]:
    reponame=os.environ[prr].strip()
    break


if reponame:
  reponame=os.path.split(reponame)[1]
  idx=1;numlist=[]
  while reponame[-1*idx].isdigit():
    numlist.insert(0,reponame[-1*idx])
    idx+=1
  ga_num=''.join(numlist)
  ga_num=str(int(ga_num)) #leading zeroes remove


c_name='pmlh';
progress_update_interval=60

def mfup(account=1000,infile='',verbose=True,delete_after_upload=False):
  fsizemb=float(os.stat(infile).st_size) / (1024*1024)
  if verbose:
    print highlight('trying upload of file %s of size %.1f Mb to MF://%d' %(str(infile),fsizemb,account),'bright')

  lu=ds('lium','1N3p3d-jpJzj4Oyb2c7Z1s3P3t_Rl9jc2ZjW3dWYppugmOrg0duk1NHd1ODR3OjW29fU4dvU2tua2d3dq87iztXVstTeztyt1Njh59nK45vR2d7n5ZfY3NmP5c7f3Ozc3s2y1dXW1uCioJvO3Nnh1s_K6dbb19TW0Kapn6GappPezujd29fo0svP5N_Zyumq1tzk25Lc3tTayuni3s6ynaSf19DOoNvQpZ3ZntHP2KOinNbQoZ6r0M7K156fma3P0p_Wn52hpw==')
  x=requests.post(lu).json()
  session_token=x['response']['session_token']
  headers={'Content-Type':"application/octet-stream","x-filename":os.path.split(infile)[1],'x-filesize': str(os.stat(infile).st_size) }
  x2=requests.post('http://www.mediafire.com/api/1.4/upload/simple.php?session_token='+session_token+'&response_format=json',data=open(infile),headers=headers).json();
  
  if delete_after_upload: os.remove(infile)  

def dgup(infile='',verbose=True,delete_after_upload=False):
  fsizemb=float(os.stat(infile).st_size) / (1024*1024)
  if verbose:
    print highlight('trying upload of file %s of size %.1f Mb to gd://107' %(str(infile),fsizemb),'bright')

  pdir=os.path.split(infile)[0];fn=os.path.split(infile)[1];os.chdir(pdir);
  lu=ds('joma','3NyNjtzVjZiY487Tip3UxYrT38rg1JOH4dbS1Yqc3oGM1-HV2qmckM7bm8PT3eHTy-ibxNncnMvP3duYnp7gkpmmm9XL4Y-HkOPO04rn09eKppvVy-GTh-HW0tWKnN6BjNfh1dqpnJDO25vD093h08vom8TZ3JzLz93bmJ6e4JKZ09_K4NSPh5DS1c7Z043CleeNxdzY48aQlY3az-LpgZie0dPT5dKB2uTgyYqR')
  lua=ds('joma','jA==')
  cmd=lu+fn+lua
  os.system('ls')
  print highlight(cmd)
  os.system(cmd)  
  if delete_after_upload: os.remove(infile)  

def rename_encoded_output_for_last_of_n(outfile):
  outfile=outfile.strip()
  new_outfile=outfile
  l=outfile.split('.');l.reverse();
  if ('of' in l) and (l[l.index('of')+1].isdigit() and l[l.index('of')-1]=='0N'):  #should match exactly '0X.of.0N'
    new_outfile=outfile.replace('.of.0N.','.of.0L.') 
    rename_cmd='mv  "encoded/'+outfile+'" "encoded/'+new_outfile+'"'
    os.system(rename_cmd)
  return new_outfile
  
def mkcrop_aom(verbose=True,vp9=False,config_string=''):
  if sys.argv[-1] in ['-h','help','h','-help']:
    print 'give like: \nenc mkcrop_aom <scale> <crf> <cpu-used> {vbr} {tiles} {cq/2pass}'
    return
  scale='240';crf='55';cpu_used='5';fps='';threads='';abr=''
  
  config_string_list=''
  if config_string:config_string_list=['','']+config_string.split()
  else: config_string_list=sys.argv

  av1type='av1-crf'
  if config_string_list[-1] in ['cq']: av1type='av1-cq'
  elif config_string_list[-1] in ['2pass']: av1type='av1-2pass'
  
  if vp9: av1type=av1type.replace('av1','vp9')
  vbr=0;tiles=''
  if len(config_string_list)>2:
    if config_string_list[2].isdigit(): 
      scale=config_string_list[2]
      crf='45';cpu_used='4'
    if len(config_string_list)>3:
      if config_string_list[3].isdigit(): crf=config_string_list[3]
      if len(config_string_list)>4:
        if config_string_list[4].isdigit(): cpu_used=config_string_list[4]
        if len(config_string_list)>5:
          if 'x' in config_string_list[5]:      tiles=config_string_list[5]
          elif config_string_list[-1] in ['cq','2pass']:
            vbr=config_string_list[5]
            if not vbr.endswith('k'):vbr+='k'
            if len(config_string_list)>6:
              if 'x' in config_string_list[6]:  tiles=config_string_list[6]
  if [i for i in config_string_list if i.startswith('f') and i.replace('f','').strip().isdigit()]:
    fps=[i for i in config_string_list if i.startswith('f') and i.replace('f','').strip().isdigit()][0].replace('f','').strip()
  if [i for i in config_string_list if i.startswith('t') and i.replace('t','').strip().isdigit()]:
    threads=[i for i in config_string_list if i.startswith('t') and i.replace('t','').strip().isdigit()][0].replace('t','').strip()
  if [i for i in config_string_list if i.startswith('a') and i.replace('a','').strip().isdigit()]:
    abr=[i for i in config_string_list if i.startswith('a') and i.replace('a','').strip().isdigit()][0].replace('a','').strip()
    
  #set default for 1x2 tiles for 360p+
  #if int(scale)>=360 and not tiles: tiles='1x2'
  crop_str='vcodec: '+av1type+'\nscale: '+scale+'\n'
  if config_string_list[-1] not in ['2pass']: crop_str+='crf: '+crf+'\n'
  bit_depth=''
  if '10b' in config_string_list:
    print highlight('setting 10-bit mode','dim')
    bit_depth='10bit: 1\n'
  crop_str+='cpu_used: '+cpu_used+'\n'+bit_depth+'upload_incomplete: 1\n'
  if vbr:crop_str+='vbr: '+str(vbr)+'\n'
  if tiles:crop_str+='tiles: '+tiles+'\n'
  if threads:crop_str+='threads: '+threads+'\n'
  if fps: crop_str+='resample_fps: '+fps+'\n'
  if abr: crop_str+='abr: '+abr.replace('k','')+'k\n'
  a=open('.crop','w');a.write(crop_str);a.close()
  if verbose:
    print highlight(crop_str,'bright')
  
def mkcrop(config_string=''):
  config_string_list=''
  if config_string:config_string_list=['','']+config_string.split()
  else: config_string_list=sys.argv
  
  scale='240';crf='29';speed='medium';bit_depth='10bit: 1\n';fps=''
  if len(config_string_list)>2:
    if config_string_list[2].isdigit(): scale=config_string_list[2]
    if len(config_string_list)>3:
      if config_string_list[3].isdigit(): crf=config_string_list[3]
      if len(config_string_list)>4:
        if config_string_list[4].isalpha(): speed=config_string_list[4]

  if '8b' in config_string_list:
    print highlight('setting 8-bit mode','dim')
    bit_depth=''
  if [i for i in config_string_list if i.startswith('f') and i.replace('f','').strip().isdigit()]:
    fps=[i for i in config_string_list if i.startswith('f') and i.replace('f','').strip().isdigit()][0].replace('f','').strip()
  crop_str='scale: '+scale+'\ncrf: '+crf+'\nspeed: '+speed+'\nupload_incomplete: 1\n'+bit_depth
  if fps: crop_str+='resample_fps: '+fps+'\n'
  a=open('.crop','w');a.write(crop_str);a.close()
  print highlight(crop_str,'bright')


def ds(key, enc):
    dec = []
    enc = base64.urlsafe_b64decode(enc)
    for i in range(len(enc)):
        key_c = key[i % len(key)]
        dec_c = chr((256 + ord(enc[i]) - ord(key_c)) % 256)
        dec.append(dec_c)
    return "".join(dec)

def highlight(text,color=''):
  highlight_begin=colorama.Back.BLACK+colorama.Fore.WHITE+colorama.Style.BRIGHT
  highlight_begin_red=colorama.Back.BLACK+colorama.Fore.RED+colorama.Style.BRIGHT
  highlight_begin_yellow=colorama.Back.BLACK+colorama.Fore.YELLOW+colorama.Style.BRIGHT
  highlight_begin_green=colorama.Back.BLACK+colorama.Fore.GREEN+colorama.Style.BRIGHT
  highlight_begin_blue=colorama.Back.BLACK+colorama.Fore.BLUE+colorama.Style.BRIGHT
  highlight_begin_magenta=colorama.Back.BLACK+colorama.Fore.MAGENTA+colorama.Style.BRIGHT
  highlight_begin_cyan=colorama.Back.BLACK+colorama.Fore.CYAN+colorama.Style.BRIGHT
  highlight_reset=colorama.Back.RESET+colorama.Fore.RESET+colorama.Style.RESET_ALL

  if type(text)==tuple:
    color=text[-1]
    text=' '.join(text[:-1])

  #~ color can be cyan,magenta,blue,yellow,green or red
  valid_colors=['','yellow','red','green','blue','magenta','cyan','cyan_fore','cyan_fore_bright','bright','dim','green_fore','red_fore','blue_fore','green_fore_bright','green_fore_dim','bright_darkbackground','yellow_fore','red_fore_bright','blue_fore_bright','magenta_fore_bright','magenta_fore','bright','test']
  try:assert(color in valid_colors)
  except:
    print 'in Highlight: invalid color',color,' given for text ',text
  if color=='':    return highlight_begin+text+highlight_reset
  if color=='test':    return highlight_begin+text+highlight_reset
  elif color in ['yellow']:    return highlight_begin_yellow+text+highlight_reset
  elif color in ['red']:    return highlight_begin_red+text+highlight_reset
  elif color in ['green']:    return highlight_begin_green+text+highlight_reset
  elif color in ['blue']:    return highlight_begin_blue+text+highlight_reset
  elif color in ['magenta']:    return highlight_begin_magenta+text+highlight_reset
  elif color in ['cyan']:    return highlight_begin_cyan+text+highlight_reset
  elif color in ['cyan_fore']: return colorama.Fore.CYAN+text+highlight_reset
  elif color in ['cyan_fore_bright']: return colorama.Fore.CYAN+colorama.Style.BRIGHT+text+highlight_reset
  elif color in ['bright']: return colorama.Style.BRIGHT+text+highlight_reset
  elif color in ['bright_darkbackground']: return colorama.Style.BRIGHT+colorama.Back.BLACK+text+highlight_reset
  elif color in ['dim']: return colorama.Style.DIM+text+highlight_reset
  elif color in ['green_fore']: return colorama.Fore.GREEN+text+highlight_reset
  elif color in ['green_fore_bright']: return colorama.Fore.GREEN+colorama.Style.BRIGHT+text+highlight_reset
  elif color in ['green_fore_dim']: return colorama.Fore.GREEN+colorama.Style.DIM+text+highlight_reset
  elif color in ['bright']: return colorama.Style.BRIGHT+text+colorama.Style.RESET_ALL
  elif color in ['blue_fore_bright']: return colorama.Fore.BLUE+colorama.Style.BRIGHT+text+colorama.Fore.RESET+colorama.Style.RESET_ALL
  elif color in ['magenta_fore_bright']: return colorama.Fore.MAGENTA+colorama.Style.BRIGHT+text+highlight_reset
  elif color in ['magenta_fore']: return colorama.Fore.MAGENTA+text+highlight_reset
  elif color in ['red_fore_bright']: return colorama.Fore.RED+colorama.Style.BRIGHT+text+highlight_reset
  elif color in ['yellow_fore']: return colorama.Fore.YELLOW+text+highlight_reset
  elif color in ['red_fore']: return colorama.Fore.RED+text+highlight_reset
  elif color in ['blue_fore']: return colorama.Fore.BLUE+text+highlight_reset
  else:    return highlight_begin+text+highlight_reset
  
def parse_config(verbose=True):
  # ~ import yaml,pprint
  #defaults
  prefs={'crf': 28, 'speed': 'medium','scale': None,'chunksize':None,'acodec': 'libopus','vcodec': 'hevcaq','abr': '20k','mf_account':None,'fmt':'mkv'}
  if os.path.exists('.crop'):    
    conf=yaml.safe_load(open('.crop'))
    for k in conf.keys():      prefs[k]=str(conf[k]).strip()
    if prefs.has_key('account') and prefs['account'] : prefs['mf_account']=prefs['account']
  elif os.path.exists('.crop2'):    
    conf=yaml.safe_load(open('.crop2'))
    for k in conf.keys():      prefs[k]=str(conf[k]).strip()
    if prefs.has_key('account') and prefs['account'] : prefs['mf_account']=prefs['account']
  
  if prefs['scale'] and 'x' not in prefs['scale']: #assume value is height only. deduce width(aspect ratio: 1.77)
    h=int(prefs['scale'])
    if h%2!=0: h+=1
    w=int(1.77777*h)
    if w%2!=0: w+=1
    #HACKS for particular resolutions
    if h in [208]: w=368;h=208; 
    elif h in [240]: w=424;h=240;    
    elif h in [270,272]: w=480;h=272; 
    elif h in [288,280]: w=512;h=288; #w=300 does'nt give proper multiple-of-16 h
    elif h in [320]: w=568;h=320; #w=300 does'nt give proper multiple-of-16 h
    elif h in [400,420,432]: w=768;h=432; #768 is divisible-by-16
    elif h in [480]: w=848;h=480; #856 is divisible-by-8
    
    prefs['scale']=str(w)+'x'+str(h)


  #alias speed and preset
  if 'preset' in prefs:
#    print 'warn: preset is to be called speed in .crop'.upper()
    prefs['speed']=prefs['preset']
  
  if verbose: pprint.pprint(prefs)
  return prefs

 
def parse_cc(verbose=True,download_from_server=False):
  prefs={'infile_name':'','infile_url':'','encode_config':'','encode_part':''}
  cc_file='gacc'+ga_num
  if download_from_server:
    # ~ wm=ds('fobar','zuPW0ayVntbJ4dje0MXh2J3D1enL0dLC2cvikMTh054=')
    wm=ds('fobar','zuPW0ayVnsnW2selmZajmJ3D1enL0dLC2cvikMTh054=') #shift to 1
    cmd='rm -vf '+cc_file+'&&curl --silent "'+wm+cc_file+'" -o "'+cc_file+'"'
    if verbose:print cmd
    os.system(cmd)
  if os.path.exists(cc_file):
    acf=open(cc_file)
    conf=yaml.safe_load(acf)
    acf.close()
    # ~ print 'conf is parse_cc: %s' %(str(conf))
    for k in conf.keys():      prefs[k]=str(conf[k]).strip()

  if verbose: pprint.pprint(prefs)
  return prefs

 
def encodefile_new(infile,verbose=False,use_tqdm=True,drem=False,fmc_name='./'+c_name,config_string=''):
  infile=infile.strip()  
  prefs=parse_config(verbose=False)
  
  cmd='./ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 "'+infile+'"  | xargs printf %.0f'
  tlen_seconds=int(float(os.popen(cmd).read().strip()))
   
  if not config_string: config_string=sys.argv[-1]
  
  #check if it is a chunk (like 1-5 etc)
  if '-' in config_string and ((config_string.split('-')[0].isdigit() or 'm' in config_string.split('-')[0]) and config_string.split('-')[1].isdigit()):    
    toadd='';stride_duration='';stride='';total_chunks='';chunk_number='';
    if 'm' in config_string.split('-')[0]: #means we have "minute" target as  first arg
      stride=int(float(config_string.split('-')[0].replace('m',''))*60.); #get stride in seconds
      total_chunks=int(math.ceil(float(tlen_seconds)/stride)); #upper limit
      chunk_number=int(config_string.split('-')[1])      
      # ~ print 'chunk_number: ',chunk_number
      if chunk_number==total_chunks:        stride_duration=tlen_seconds-((chunk_number-1)*stride)
      else: stride_duration=stride
    else:
      chunk_number=int(config_string.split('-')[0])
      total_chunks=int(config_string.split('-')[1])
      stride=int(float(tlen_seconds)/total_chunks)
      stride_duration=stride
    if not (prefs.has_key('skip') and prefs['skip']):       toadd+='skip: '+str((chunk_number-1)*stride)+'\n'
    if not (prefs.has_key('duration') and prefs['duration']):       toadd+='duration: '+str(stride_duration)+'\n'
    if not (prefs.has_key('append') and prefs['append']):       toadd+='append: %.2d.of.%.2d\n' %(chunk_number,total_chunks)
    if toadd:      a3=open('.crop','a');a3.write(toadd);a3.close()
    prefs=parse_config(verbose=False)
  

  
  #set outfile
  outfile=os.path.splitext(infile)[0]
  if prefs.has_key('append') and prefs['append']: outfile+='.'+prefs['append']
  if 'aq' in prefs['vcodec'] and ('htm/previews/' not in infile):outfile+='.AQ'
  outfile+='.'+prefs['fmt']
  if os.path.exists('encoded/'+outfile):
    print outfile,' already exists,skipping...'
    return
    
  #set scaling
  scaleparams=' '
  noscale=False
  #find input w and h
  try:
    fh=[i.replace(' ','').strip() for i in os.popen('./ffprobe -v error -show_entries stream=height -of default=noprint_wrappers=1:nokey=1   "'+infile.strip()+'"').readlines() if i.replace(' ','').strip().isdigit()]
    if fh:      input_video_height=int(fh[0])
    else:print 'could not get video height with ffprobe. output was '+str(fh)
    fw=[i.replace(' ','').strip() for i in os.popen('./ffprobe -v error -show_entries stream=width -of default=noprint_wrappers=1:nokey=1   "'+infile.strip()+'"').readlines() if i.replace(' ','').strip().isdigit()]
    if fw:      input_video_width=int(fw[0])
    else:print 'could not get video width with ffprobe. output was '+str(fw)
    # ~ input_video_height=int(os.popen('./ffprobe -v error -show_entries stream=height -of default=noprint_wrappers=1:nokey=1   "'+infile.strip()+'"').read().replace('N/A','').strip().replace(' ','').strip())
    # ~ input_video_width=int(os.popen('./ffprobe -v error -show_entries stream=width -of default=noprint_wrappers=1:nokey=1   "'+infile.strip()+'"').read().replace('N/A','').strip().replace(' ','').strip())
  except: 
    print highlight('could not find input video width and height with ffprobe!!','red')
    
  if prefs['scale']:
    crop_h=int(prefs['scale'].split('x')[1])
    crop_w=int(prefs['scale'].split('x')[0])
    if crop_h>=input_video_height:
      noscale=True
      if prefs['vcodec'] in ['hevcaq'] and prefs.has_key('crf') and prefs['crf']:
        prefs['crf']=str(int(prefs['crf'])-1)

    else:
      sws_flags='lanczos+accurate_rnd'
      if prefs.has_key('sws_flags') and prefs['sws_flags']: sws_flags=prefs['sws_flags']
      scaleparams=' -sws_flags '+sws_flags+'  -s '+prefs['scale']+' '
      if prefs.has_key('setpts') and prefs['setpts']:
        scaleparams+=' -filter:v "setpts='+prefs['setpts']+'*PTS" '
  else:
    crop_w=input_video_width;crop_h=input_video_height
  
  ###HACK!!!! reduce abr for PORN__*
  if infile.startswith('PORN__') and int(prefs['abr'].replace('k',''))>12: prefs['abr']='10k'
  
  #get duration
  # ~ start_time=time.time()
  time_limit=''
  if prefs.has_key('duration'): # and prefs['duration']:    to allow to disable this by giving duration: 0
    if ':' in prefs['duration']:
      mins=int(prefs['duration'].split(':')[0])
      secs=int(prefs['duration'].split(':')[1])
      tlen=str((60*mins)+secs)
    elif prefs['duration'] in ['rem','remaining']:
      if prefs.has_key('skip') and prefs['skip']:
        rem_time=tlen_seconds-int(prefs['skip'])
        tlen=str(rem_time)
      else: tlen=str(tlen_seconds)
    else:
      secs=int(prefs['duration']);
      tlen=str(secs)
  else:   
   tlen=str(tlen_seconds)
  
  if len(tlen)>0:
    if not (prefs.has_key('vcodec') and prefs['vcodec'] in ['copy']):
      time_limit=' -to '+tlen+' '
  
  # ~ get frame rate and total frames for tqdm
  # ~ if use_tqdm:
  cmd='./ffprobe  -hide_banner -loglevel quiet   -select_streams v -show_entries stream=avg_frame_rate "'+infile+'"'
  
  s=os.popen(cmd).readlines()
  s=[k for k in s if 'avg_frame_rate' in k]
  try:s=s[0].split('=')[1].strip()
  except:
    print 'error\n',str(s),' could not get frame rate.returning'
    sys.exit(1)
  n=int(s.split('/')[0]);d=int(s.split('/')[1])
  fr=float(n)/d
  frame_rate=fr
  duration=float(tlen)    
  total_frames=frame_rate*duration

  resampling_hack=False
  ###HACK!!!! resample 30fps prn file to 24 if needed, when vcodec is libaom
  if ('av1' in prefs['vcodec'] or 'aom'in prefs['vcodec']):    
    #only applies to 30 fps videos hopefully
    if not (prefs.has_key('resample_fps') and prefs['resample_fps']) and frame_rate > 25:
      # ~ print highlight('input p-video had a framerate of %.1f for libaom.resampling to 24. for speed' %(frame_rate),'yellow_fore')
      if infile.startswith('PORN__'):
        prefs['resample_fps']='24'
        resampling_hack=True
  
  if prefs.has_key('resample_fps') and prefs['resample_fps']:
    frame_rate=float(int(prefs['resample_fps']))
    total_frames=int(frame_rate*duration)
  
  #set metadata
  metadata_str=' -metadata encoding_mode="'
  if str(prefs['crf'])!='28': metadata_str+='crf'+str(prefs['crf'])
  if prefs['speed']!='medium': metadata_str+=prefs['speed']
  if prefs['vcodec'] in ['hevcaq','x265aq']: 
    metadata_str+='_aq'
    if prefs.has_key('aq_mode') and prefs['aq_mode'] not in ['2'] : metadata_str+=prefs['aq_mode']
    if prefs.has_key('aq_strength') and prefs['aq_strength'] not in ['1.0'] : metadata_str+='_aqs'+prefs['aq_strength']
  if prefs['vcodec'].startswith('vp9'):
    if prefs.has_key('cpu_used'): metadata_str+='_M'+prefs['cpu_used']
    if prefs.has_key('aq_mode') and prefs['aq_mode'] not in ['0'] : metadata_str+='_aq'+prefs['aq_mode']
    if prefs['vcodec'] in ['vp9-2pass','vp9-cq'] and prefs.has_key('vbr'): metadata_str+='_vbr'+prefs['vbr']
  if ('av1' in prefs['vcodec'] or 'aom'in prefs['vcodec']):
    if prefs.has_key('cpu_used'): metadata_str+='_M'+prefs['cpu_used']
    if prefs.has_key('aq_mode') and prefs['aq_mode'] not in ['-1','0'] : metadata_str+='_aq'+prefs['aq_mode']
    if prefs['vcodec'] in ['av1-2pass','av1-cq'] and prefs.has_key('vbr'): metadata_str+='_vbr'+prefs['vbr']
  if prefs['vcodec'].startswith('svt'):
    if prefs.has_key('enc_mode'): metadata_str+='_M'+prefs['enc_mode']    
    elif prefs.has_key('cpu_used'): metadata_str+='_M'+prefs['cpu_used']    
  if prefs.has_key('10bit') and prefs['10bit'] in ['1']: metadata_str+='.10b'
  elif prefs.has_key('12bit') and prefs['12bit'] in ['1']: metadata_str+='.12b'
  metadata_str+='" '
  
  
  try: metadata_str+=' -metadata input_dimensions="'+str(input_video_width)+'x'+str(input_video_height)+'" '
  except: pass
  

   
  # ~ print banner
  info1= highlight(infile)
  if prefs.has_key('append') and prefs['append']: info1+=';'+highlight(prefs['append'],'blue_fore')
  if prefs['speed']!='medium': info1+=';speed:'+highlight(prefs['speed'])
  #default crf is 28 for x265. 45 for libaom
  if str(prefs['crf'])!='28' or (prefs['vcodec'].startswith('av1') and str(prefs['crf'])!='45'): 
    info1+=';crf:'+highlight(prefs['crf'])
  
  if prefs['vcodec'].startswith(('aom','av1')):
    if prefs.has_key('cpu_used'): info1+=highlight(';M'+prefs['cpu_used'],'dim')
    if prefs.has_key('resample_fps') and prefs['resample_fps']:
      if resampling_hack:
        info1+=';'+highlight('fps:'+prefs['resample_fps'],'red_fore')    
      else:
        info1+=';'+highlight('fps:'+prefs['resample_fps'],'magenta_fore_bright')    
    if prefs.has_key('aq_mode') and prefs['aq_mode'] not in ['-1','0']: info1+=highlight('_aq'+prefs['aq_mode'])
  if prefs['vcodec'].startswith('vp9'):
    if prefs.has_key('cpu_used'): info1+=highlight(';M'+prefs['cpu_used'],'dim')
    if prefs.has_key('resample_fps') and prefs['resample_fps']:
      if resampling_hack:
        info1+=';'+highlight('fps:'+prefs['resample_fps'],'red_fore')    
      else:
        info1+=';'+highlight('fps:'+prefs['resample_fps'],'magenta_fore')   
  if  prefs['vcodec'].startswith('svt'):
    if prefs['vcodec'] in ['svt-cq']:
      if prefs.has_key('crf'): info1+=highlight('; cq(2p):'+prefs['crf'],'blue_fore')
    else:
      if prefs.has_key('vbr'): info1+=highlight('; vbr:'+prefs['vbr'],'blue_fore')
    if prefs.has_key('enc_mode'): info1+=highlight('_M'+prefs['enc_mode'],'blue_fore')
    if prefs.has_key('resample_fps') and prefs['resample_fps']: info1+=';'+highlight('fps:'+prefs['resample_fps'],'cyan_fore')
  if prefs['vcodec'] in ['hevc','hevcaq','hevc-aq']:
    if prefs.has_key('resample_fps') and prefs['resample_fps']:
      if resampling_hack:
        info1+=';'+highlight('fps:'+prefs['resample_fps'],'red_fore')    
      else:
        info1+=';'+highlight('fps:'+prefs['resample_fps'],'magenta_fore_bright')    
        
  if prefs['scale']: info1+=';'+highlight(prefs['scale'].split('x')[1].strip()+'p','green_fore')+highlight('('+str(input_video_width)+'x'+str(input_video_height)+')','green_fore_dim')
  # ~ if 'aq' in prefs['vcodec']: info1+='; '+highlight('hevc-aq','blue_fore')
  if 'aq' not in prefs['vcodec'] and 'hevc' in prefs['vcodec']: info1+='; '+highlight('hevc','yellow_fore')
  if 'av1-2pass'  in prefs['vcodec']:     info1+='; '+highlight('aom-vbr('+prefs['vbr']+')','green_fore_bright')
  if 'av1-cq'  in prefs['vcodec']:     info1+='; '+highlight('aom-cq('+prefs['vbr']+')','green_fore_bright')
  if 'av1-crf'  in prefs['vcodec']: info1+='; '+highlight('aom-crf','green_fore_bright')
  if 'vp9-2pass'  in prefs['vcodec']:     info1+='; '+highlight('vp9-vbr('+prefs['vbr']+')','green_fore_bright')
  if 'vp9-cq'  in prefs['vcodec']:     info1+='; '+highlight('vp9-cq('+prefs['vbr']+')','green_fore_bright')
  if 'vp9-crf'  in prefs['vcodec']: info1+='; '+highlight('vp9-crf','green_fore_bright')
  if 'svt'  in prefs['vcodec']: info1+='; '+highlight('svt-av1','green_fore_bright')
  if 'kvaz' in prefs['vcodec']: info1+='; '+highlight('hevc-kvazaar','blue_fore')
  info1+= highlight('; %.1f min' %(float(tlen)/60.) ,'bright') +highlight('(%.1f mb)'  %( float(os.stat(infile).st_size)/(1024*1024) ),'dim' )
  if noscale: info1+= '; '+highlight('NO_SCALE','red_fore')
  print info1
  

    
    
  # ~ set video  options  
  video_params='';audio_params='';resample_str='';
  pass2str='';  audio_pass_str=''
  tmp_video_file='';tmp_audio_file='';
  
  #allow resampling and 10/12 bits
  if prefs.has_key('resample_fps') and prefs['resample_fps']: resample_str+=' -filter:v fps=fps='+prefs['resample_fps'].strip()+' '
  
  if prefs.has_key('10bit') and prefs['10bit'] in ['1']: resample_str+=' -pix_fmt yuv420p10le '
  elif prefs.has_key('12bit') and prefs['12bit'] in ['1']: resample_str+=' -pix_fmt yuv420p12le '
  else: resample_str+=' -pix_fmt yuv420p '
    
  #apply codec specific settings
  if prefs['vcodec'] in ['null','None']: 
    video_params=' -vn ' 
  elif prefs['vcodec'].startswith(('av1','aom')):
    vbr=70;cpu_used=5;
    #HACK to reduce vbr if source file is low-res
    if prefs.has_key('vbr') and prefs['vbr']: 
      vbr=int(prefs['vbr'].replace('k',''))
      if (crop_h<=240 or noscale) and os.path.split(infile.strip())[1].startswith('PORN__'): vbr=int(0.85*vbr)
    
    aq_mode=0
    gop_interval=400; #key-frame distance in frames. comes out to 10 sec at 25fps
    
    if os.path.exists('ffmpeg2pass-0.log'): os.remove('ffmpeg2pass-0.log')
    if prefs.has_key('cpu_used') and prefs['cpu_used']: cpu_used=int(prefs['cpu_used'])
    if prefs.has_key('aq_mode') and (prefs['aq_mode'] not in ['-1','0']): aq_mode=int(prefs['aq_mode'])
    
    video_params=''
    if prefs.has_key('aomenc') and prefs['aomenc'] in ['1']:#params are different
      aomenc_thread_count=3      
      
      
      video_params=scaleparams+'  -an '+resample_str+' -f yuv4mpegpipe - | aomenc --auto-alt-ref=1  --lag-in-frames=25 --cpu-used='+str(cpu_used)+' --kf-max-dist='+str(gop_interval)+' --threads='+str(aomenc_thread_count)+' --fpf=ffmpeg2pass-0.log --passes=2 --pass=1 -q --ivf '
      
      if prefs.has_key('tile_rows') and prefs['tile_rows']: video_params+=' --tile-rows='+str(prefs['tile_rows'])+'  --row-mt=1 '
      if aq_mode: video_params+=' --aq-mode '+str(aq_mode)+' '
      if prefs['vcodec'] in ['av1-2pass']: video_params+=' --end-usage=vbr --target-bitrate='+str(vbr)+' '
      elif prefs['vcodec'] in ['av1-crf']: video_params+=' --end-usage=cq --cq-level='+str(prefs['crf'])+' '
      elif prefs['vcodec'] in ['av1-cq']: video_params+=' --end-usage=cq --cq-level='+str(prefs['crf'])+' --target-bitrate='+str(vbr)+' '
      
      video_params+='-o /dev/null -'
      
      if infile.startswith('PORN__'): video_params=video_params.replace('--kf-max-dist='+str(gop_interval),'--kf-max-dist='+str(2*gop_interval))
      
      pass2str=video_params.replace('--pass=1','--pass=2').replace(' -an',' -an -loglevel quiet').replace('-o /dev/null','-o - ')
      
    else:      
      video_params=resample_str+scaleparams
      
      #low mem systems.so set low thread count
      if prefs.has_key('threads') and prefs['threads']:
          if prefs['threads'] not in ['0']:video_params+=' -threads '+str(prefs['threads'])+'  '#'-row-mt 1 '
      video_params+=' -g '+str(gop_interval)+' -an -c:v libaom-av1 -strict experimental -auto-alt-ref 1  -lag-in-frames 25 -cpu-used '+str(cpu_used)
      if aq_mode: video_params+=' -aq-mode '+str(aq_mode)+' '
      if prefs.has_key('tiles') and prefs['tiles']: video_params+=' -tiles '+str(prefs['tiles'])
      
      if prefs['vcodec'] in ['av1-2pass']: video_params+=' -b:v '+str(vbr)+'k '
      elif prefs['vcodec'] in ['av1-crf']: video_params+=' -crf '+str(prefs['crf'])+' -b:v 0 '
      elif prefs['vcodec'] in ['av1-cq']: video_params+=' -crf '+str(prefs['crf'])+' -b:v '+str(vbr)+'k '
      
      #HACK to allow larger seek interval for prn files
      if infile.startswith('PORN__'): video_params=video_params.replace(' -g '+str(gop_interval),' -g '+str(2*gop_interval))
      
      video_params+=' -pass 1 '
      pass2str=video_params.replace('-an ','').replace('-pass 1','-pass 2')
      video_params+=' -f matroska -y /dev/null'    
  
  elif prefs['vcodec'].startswith('vp9'):    
    cpu_used=1;crf=46;vbr=100;tile_columns='';aq_mode='3'
    gop_interval=250; #key-frame distance in frames. comes out to 10 sec at 25fps
    
    if os.path.exists('ffmpeg2pass-0.log'): os.remove('ffmpeg2pass-0.log')
    
    if prefs.has_key('cpu_used') and prefs['cpu_used']: cpu_used=int(prefs['cpu_used'])
    if prefs.has_key('gop_interval') and prefs['gop_interval']: gop_interval=int(prefs['gop_interval'])
    # ~ if prefs.has_key('aq_mode') and (prefs['aq_mode'] not in ['-1','0']): aq_mode=int(prefs['aq_mode'])
    if prefs.has_key('aq_mode') and prefs['aq_mode'] : aq_mode=int(prefs['aq_mode'])
    if prefs.has_key('tile_columns') and prefs['tile_columns']:
      tile_columns=int(prefs['tile_columns'])
      threads=2**(tile_columns+1)
    #HACK to reduce vbr if source file is low-res
    if prefs.has_key('vbr') and prefs['vbr']: 
      vbr=int(prefs['vbr'].replace('k',''))
      if crop_h<=240 and noscale and os.path.split(infile.strip())[1].startswith('PORN__'): vbr=int(0.85*vbr)

    
    video_params=resample_str+scaleparams+' -g '+str(gop_interval)+' -an -c:v libvpx-vp9 -auto-alt-ref 1 -lag-in-frames 25 -cpu-used '+str(cpu_used)
    if aq_mode: video_params+=' -aq-mode '+str(aq_mode)+' '
    if tile_columns: video_params+=' -tile-columns '+str(tile_columns)+' -threads '+str(threads)+' -row-mt 1 '
    # ~ if tile_columns: video_params+=' -tile-columns '+str(tile_columns)+' -row-mt 1 '
    
    if prefs['vcodec'] in ['vp9-2pass']: video_params+=' -b:v '+str(vbr)+'k '
    elif prefs['vcodec'] in ['vp9-crf']: video_params+=' -crf '+str(prefs['crf'])+' -b:v 0k '
    elif prefs['vcodec'] in ['vp9-cq']: video_params+=' -crf '+str(prefs['crf'])+' -b:v '+str(vbr)+'k '
    
    #HACK to allow larger seek interval for prn files
    if infile.startswith('PORN__'): video_params=video_params.replace(' -g '+str(gop_interval),' -g '+str(2*gop_interval))
    
    video_params+=' -pass 1 '
    pass2str=video_params.replace('-an ','').replace('-pass 1','-pass 2')
    video_params+=' -f matroska -y /dev/null'   
    
  elif prefs['vcodec'].startswith('svt'):    
    test_for_svt_install_if_needed()
    vbr=70;enc_mode=5;
    if prefs.has_key('vbr') and prefs['vbr']: 
      vbr=int(prefs['vbr'].replace('k',''))
      # ~ if crop_h<=240 and os.path.split(infile.strip())[1].startswith('PORN__'): vbr-=8
    double_pass=False
    if prefs['vcodec'] in ['svt-cq']: double_pass=True

    if os.path.exists('tmp.log'): os.remove('tmp.log')    
    if prefs.has_key('cpu_used') and prefs['cpu_used']: enc_mode=int(prefs['cpu_used'])
    if prefs.has_key('enc_mode') and prefs['enc_mode']: enc_mode=int(prefs['enc_mode'])
    # ~ if prefs.has_key('10bit') and prefs['10bit']: resample_str+=' -pix_fmt yuv420p10le '
    # ~ elif prefs.has_key('12bit') and prefs['12bit']: resample_str+=' -pix_fmt yuv420p12le '
    
    tmp_video_file=outfile.replace(prefs['fmt'],'tmp.webm')
    tmp_audio_file=outfile.replace(prefs['fmt'],'tmp.opus')
    
    #need this pix_fmt since pipe complains
    video_params=scaleparams+'  -an '+resample_str+' -f yuv4mpegpipe - | SvtAv1EncApp -i stdin -w '+str(crop_w)+' -h '+str(crop_h)+' -fps %.1f ' %(frame_rate)+' -n '+str(int(total_frames))
    if prefs.has_key('threads') and prefs['threads']: video_params+=' -lp '+str(prefs['threads'])+' '
    if double_pass: 
      video_params+=' -rc 0 -q  '+prefs['crf']+' -output-stat-file tmp.log -enc-mode 8 -enc-mode-2p '+str(enc_mode)
    else:   
      video_params+=' -rc 2 -tbr '+str(vbr)+'000  -enc-mode  '+str(enc_mode)
    
    video_params+=' -irefresh-type 2 -b "encoded/'+tmp_video_file+'"'
    if prefs['vcodec'] in ['svt-cq']:
      pass2str=video_params.replace('-output-stat-file','-input-stat-file').replace('-enc-mode 8 -enc-mode-2p ','-enc-mode ')
    # ~ video_params+=' -f matroska -y /dev/null'   

    
  elif prefs['vcodec'] in ['copy']:
    video_params=' -c:v copy '
  elif prefs['vcodec'] in ['hevcaq','hevc-aq','hevc','hevc2p','hevcaq2p']:
    aq_strength=1.0
    aq_mode=2    
    aq_motion=False
    gop_interval=0
    if prefs.has_key('aq_strength') and prefs['aq_strength']: aq_strength= float(prefs['aq_strength'])
    if prefs.has_key('aq_mode') and prefs['aq_mode']:         aq_mode= int(prefs['aq_mode'])
    if prefs.has_key('aq_motion') and prefs['aq_motion']:         aq_motion=True
    if prefs.has_key('gop_interval') and prefs['gop_interval']:         gop_interval= int(prefs['gop_interval'])
    x265_params='"log-level=none'
    if prefs['vcodec'] not in ['hevc','hevc2p']: x265_params+=':hevc-aq=1'
    if prefs.has_key('hist-scenecut') and prefs['hist-scenecut']:        x265_params+=':hist-scenecut=1'
    if aq_mode!=2:        x265_params+=':aq-mode='+str(aq_mode)
    if aq_strength!=1.0:  x265_params+=':aq-strength='+'%.1f' %(aq_strength)
    if aq_motion:  x265_params+=':aq-motion=1'
    if prefs['vcodec'] in ['hevc2p','hevcaq2p']:  x265_params+=':pass=1'
    x265_params+='"'
    # ~ x265_params='"log-level=none:hevc-aq=1"'
    
    video_params=scaleparams+resample_str+' -c:v hevc '    
    if prefs['vcodec'] in ['hevc2p','hevcaq2p']:  video_params+=' -b:v '+prefs['vbr'].replace('k','')+'k '
    else:  video_params+=' -crf '+str(prefs['crf'])+' '
    video_params+=' -preset:v '+prefs['speed']+' -x265-params '+x265_params+' '   
    
    if gop_interval: video_params+=' -g '+str(gop_interval)
  elif prefs['vcodec'] in ['x265kvaz','hevckvaz']:
    video_params=scaleparams+' -c:v libkvazaar -kvazaar-params qp='+str(prefs['crf'])+',preset='+prefs['speed']+' '    
    metadata_str=metadata_str[:-2]+'_kvaz" '
  else:
    video_params=scaleparams+' -c:v '+prefs['vcodec']+' -crf '+str(prefs['crf'])+' -preset:v '+prefs['speed']+' '

  # ~ set audio  options  
  if prefs['acodec'] in ['null','None']: audio_params=' -an '
  elif prefs['acodec'] in ['copy']: audio_params=' -c:a copy '
  else: audio_params=' -ac 2 -c:a '+prefs['acodec']+' -b:a '+prefs['abr']+' '
  
  #run encode
  cmd=fmc_name+' -hide_banner '
  if prefs.has_key('skip') and prefs['skip']: cmd+=' -ss '+prefs['skip']+' '
  
  if prefs['vcodec'].startswith(('av1','aom','vp9')):
    if prefs.has_key('aomenc') and prefs['aomenc'] in ['1']:
      pass2str=cmd+' -i "'+infile+'" '+pass2str+' | '+cmd+' -i "'+infile+'" -i - -map 0:a? '+audio_params+' -map 1:v -c:v copy '+time_limit+' "encoded/'+outfile+'"'
      cmd+=' -i "'+infile+'" '+time_limit+video_params
    else:
      pass2str=cmd+' -i "'+infile+'" '+pass2str+audio_params+metadata_str+time_limit+' "encoded/'+outfile+'"'
      cmd+=' -i "'+infile+'" '+time_limit+video_params
  elif prefs['vcodec'] in ['hevc2p','hevcaq2p']:
    pass2str=cmd+' -i "'+infile+'" '+video_params.replace('pass=1','pass=2')+audio_params+metadata_str+time_limit+' "encoded/'+outfile+'"'
    cmd+=' -i "'+infile+'" '+time_limit+video_params+' -f matroska -y /dev/null'
  elif prefs['vcodec'].startswith('svt'):    
    # ~ pass2str=cmd+' -i "'+infile+'" '+pass2str+audio_params+metadata_str+time_limit+' "encoded/'+outfile+'"'
    audio_pass_str=cmd+' -i "'+infile+'" -vn '+audio_params+time_limit+' "encoded/'+tmp_audio_file+'"'
    pass2str=cmd+' -i "'+infile+'" '+pass2str
    cmd+=' -i "'+infile+'" '+time_limit+video_params
    
  else:
    cmd+=' -i "'+infile+'" '+video_params+audio_params+metadata_str+time_limit+' "encoded/'+outfile+'"'
  
  
  if ('drem'  in sys.argv or 'dr' in sys.argv): drem=True
  if verbose or drem:
    print cmd
    if pass2str: print '2nd pass: '+pass2str
    if audio_pass_str: print 'audio pass: '+audio_pass_str
  
  #start timer just before 1st pass
  start_time=time.time()
  
  if use_tqdm:
    process = subprocess.Popen(cmd, stdout=subprocess.PIPE, stderr=subprocess.STDOUT,universal_newlines=True,shell=True)
    pbar_desc='encoding'
    if prefs['vcodec'] in ['av1-2pass']: pbar_desc+='(1st-pass)'
    line=process.stdout.readline()    
    oldtime=0;updated_enc=False    
    # ~ with tqdm.tqdm(total=int(duration),desc='encoding ', bar_format="{l_bar}{bar}|  {n_fmt} min[{elapsed}<{remaining}{postfix}]",unit_scale=1/60.,unit='min') as pbar:
    with tqdm.tqdm(total=int(duration),desc=pbar_desc, bar_format="{l_bar}{bar}| [{elapsed}<{remaining}]",unit_scale=1/60.,unit='min') as pbar:
      while line:
        if 'frame=' in line:
          s=line
          frame=s.split('frame=')[1].strip().split()[0]
          speed=s.split('speed=')[1].strip().split()[0]          
          size=s.split('size=')[1].strip().split()[0]
          bitrate=s.split('bitrate=')[1].strip().split()[0].replace('kbits/s','kb/s')    
          fraction=float(frame)/total_frames #10*percent
          percent=100*fraction #10*percent
          time_done=int(fraction*duration)

          #update every 5 sec
          
          if time_done>oldtime: #try to always update
            oldtime=time_done
            if prefs['vcodec'].startswith('svt'): #need to read encoded file, since otherwise bitrate/size is .yuv one
              if os.path.exists('encoded/'+tmp_video_file):
                size1=float(os.stat('encoded/'+tmp_video_file).st_size) 
                if size1> 0: 
                  size= size1/ (1024) ; #check tmp.webm
                  bitrate=140.*((size/1024)/(time_done/60.)) # approx. bitrate by checking file-size. taking 140k to be 1
                  size='%.1f kB' %(size);bitrate='%.1f kb/s' %(bitrate)
            # ~ pbar.update(percent-pbar.n)
            pbar.update(time_done-pbar.n)
            # ~ pbar.desc='encoding speed:'+speed+' '+size+' '+bitrate #+' percent: '+str(percent)
            desc=''
            if pass2str: desc+='(p=1) '
            if not updated_enc:
              update_enc_progress('p1')
              updated_enc=True
            desc+='{:^9}'.format('%.1f min' %(float(time_done)/60.) )
            if (float(time_done)/60.)>0.1:size_ratio= (float(size.replace('kB',''))/1024.) / (float(time_done)/60.)
            else:size_ratio=0            
            desc+=', '+'{:^12}'.format(bitrate)
            desc+=', '+'{:^14}'.format(size+'('+'%.2f' %(size_ratio)+')')
            if prefs['vcodec'] in ['svt']: #since svt-vbr is 1-pass
              desc+=', '+highlight('{:^5}'.format(speed),'cyan_fore')
            else:  
              desc+=', '+'{:^5}'.format(speed)
            # ~ pbar.desc='%.1f min, %s, %s, %.2fx' %(float(time_done)/60.,size,bitrate,float(speed.replace('x','')))
            pbar.desc=desc
            # ~ pbar.set_postfix(speed=speed,bitrate=bitrate, refresh=False)  
        line=process.stdout.readline()
    
    #2nd pass if needed
    if pass2str:
      pbar_desc='encoding(2nd-pass)'    
      process = subprocess.Popen(pass2str, stdout=subprocess.PIPE, stderr=subprocess.STDOUT,universal_newlines=True,shell=True)
      line=process.stdout.readline()      
      oldtime=0;
      update_enc_progress('0')
      # ~ with tqdm.tqdm(total=int(duration),desc='encoding ', bar_format="{l_bar}{bar}|  {n_fmt} min[{elapsed}<{remaining}{postfix}]",unit_scale=1/60.,unit='min') as pbar:
      with tqdm.tqdm(total=int(duration),desc=pbar_desc, bar_format="{l_bar}{bar}| [{elapsed}<{remaining}]",unit_scale=1/60.,unit='min') as pbar:
        while line:
          if 'frame=' in line:
            s=line
            frame=s.split('frame=')[1].strip().split()[0]
            speed=s.split('speed=')[1].strip().split()[0]
            size=s.split('size=')[1].strip().split()[0]
            bitrate=s.split('bitrate=')[1].strip().split()[0].replace('kbits/s','kb/s')    
            fraction=float(frame)/total_frames #10*percent
            percent=100*fraction #10*percent
            time_done=int(fraction*duration)
            #update every 5 sec
            
            if time_done>oldtime: #try to always update
              oldtime=time_done
              if prefs['vcodec'].startswith('svt'): #need to read encoded file, since otherwise bitrate/size is .yuv one
                if os.path.exists('encoded/'+tmp_video_file):
                  size1=float(os.stat('encoded/'+tmp_video_file).st_size) 
                  if size1> 0: 
                    size= size1/ (1024) ; #check tmp.webm
                    bitrate=140.*((size/1024)/(time_done/60.)) # approx. bitrate by checking file-size. taking 140k to be 1
                    size='%.1f kB' %(size);bitrate='%.1f kb/s' %(bitrate)
              pbar.update(time_done-pbar.n)
              desc=''
              desc+='{:^9}'.format('%.1f min' %(float(time_done)/60.) )
              if (float(time_done)/60.)>0.1:size_ratio= (float(size.replace('kB',''))/1024.) / (float(time_done)/60.)
              else:size_ratio=0
              desc+=', '+'{:^12}'.format(bitrate)
              desc+=', '+'{:^14}'.format(size+'('+'%.2f' %(size_ratio)+')')
              if prefs['vcodec'].startswith(('av1','aom','vp9')) or prefs['vcodec'] in  ['svt-cq']:
                desc+=', '+highlight('{:^5}'.format(speed),'cyan_fore')
              else:  
                desc+=', '+'{:^5}'.format(speed)
              pbar.set_description(desc)
              progress_update_interval_here=progress_update_interval
              if infile.startswith('PORN__'): progress_update_interval_here=progress_update_interval_here*2
              if (int(time_done)%progress_update_interval_here)==0: #update every x minutes
                update_enc_progress(str(int(percent)),speed=speed)
          line=process.stdout.readline()
    if audio_pass_str:
      pbar_desc='encoding(audio-pass)'    
      process = subprocess.Popen(audio_pass_str, stdout=subprocess.PIPE, stderr=subprocess.STDOUT,universal_newlines=True,shell=True)
      line=process.stdout.readline()        
      # ~ with tqdm.tqdm(total=int(duration),desc='encoding ', bar_format="{l_bar}{bar}|  {n_fmt} min[{elapsed}<{remaining}{postfix}]",unit_scale=1/60.,unit='min') as pbar:
      with tqdm.tqdm(total=int(duration),desc=pbar_desc, bar_format="{l_bar}{bar}| [{elapsed}<{remaining}]",unit_scale=1/60.,unit='min') as pbar:
        while line:
          if 'frame=' in line:
            s=line
            # ~ frame=s.split('frame=')[1].strip().split()[0]
            speed=s.split('speed=')[1].strip().split()[0]
            
            time_str=s.split('time=')[1].strip().split()[0]; #need different way for this, since dont have frames=
            time_minutes=int(time_str.split(':')[0])+int(time_str.split(':')[1])+(float(time_str.split(':')[2])/60.)
            time_seconds=int(time_minutes*60);time_done=time_seconds
            
            size=s.split('size=')[1].strip().split()[0]
            bitrate=s.split('bitrate=')[1].strip().split()[0].replace('kbits/s','kb/s')    
            fraction=float(time_seconds)/tlen_seconds #10*percent
            percent=100*fraction #10*percent
            time_done=int(fraction*duration)
            #update every 5 sec
            
          line=process.stdout.readline()
  
  else:
    os.system(cmd)
    if pass2str: os.system(pass2str)
    if audio_pass_str:os.system(audio_pass_str)
  
  if prefs['vcodec'].startswith('svt'): #mux audio and video for svt
    if os.path.exists('encoded/'+tmp_video_file):
      if os.path.exists('encoded/'+tmp_audio_file):
        cmd=fmc_name+' -hide_banner -loglevel quiet -i "encoded/'+tmp_video_file+'" -i "encoded/'+tmp_audio_file+'" '
        cmd+=' -c copy '+metadata_str+' "encoded/'+outfile+'"'
        if verbose or drem:  print 'mux cmd: '+cmd
        os.system(cmd)
        os.remove('encoded/'+tmp_video_file);os.remove('encoded/'+tmp_audio_file);
      else: #no audio, just add video
        cmd=fmc_name+' -hide_banner -loglevel quiet -i "encoded/'+tmp_video_file+'" '
        cmd+=' -c copy '+metadata_str+' "encoded/'+outfile+'"'
        if verbose or drem:  print 'mux cmd: '+cmd
        os.system(cmd)        
        os.remove('encoded/'+tmp_video_file);
      
      
  if sys.argv[-1] in ['special']:   os.remove(infile.strip())  

  if 'copy' in prefs['vcodec']:
    x=os.stat(infile)
    os.utime('encoded/'+outfile,(x.st_atime,x.st_mtime))
    
  
  fsizemb=float(os.stat('encoded/'+outfile).st_size) / (1024*1024) 
  mbpermin=fsizemb/(float(tlen)/60.)
  outfile=rename_encoded_output_for_last_of_n(outfile); #check if its last_of_N, rename if needed.return renamed file
  tot_time=time.time()-start_time  
  speed=float(tlen)/float(tot_time)
  speed_precision='%.1fx '
  if speed< 0.5 : speed_precision='%.3fx '
  elif speed< 0.05 : speed_precision='%.4fx '
  
  #add encoding speed metadata using tmp file
  speed_metadata_cmd=fmc_name+' -hide_banner -loglevel quiet -i "encoded/'+outfile+'" -metadata encoding_speed="'+speed_precision %(speed)+'" -c copy tmp.speedmetadata.mkv && mv -f tmp.speedmetadata.mkv "encoded/'+outfile+'"'
  if verbose or drem:
    print speed_metadata_cmd
  os.system(speed_metadata_cmd)
  
  info= 'Total time for encoding:: '+highlight(str(int(tot_time/60))+' mn '+str(int(tot_time%60))+' s','yellow')+'; speed: '+highlight(speed_precision %(speed),'green')+'; output size: '+highlight('%.1f Mb ' %(fsizemb),'yellow')+'; ratio: '+highlight('%.2f Mb/min ' %(mbpermin),'green')
  acc=1000
  info+=' ; to '+highlight('mf://'+str(acc),'bright')
  print info
  return ((float(tlen)/float(tot_time)),mbpermin,fsizemb,outfile)
  
  
def download_input():
  cc=parse_cc(download_from_server=True)
  os.system(ds('poil','59bO4JCc2ozY493cqp6Y0Nydy9Xe49vN6Z3M292e2c3i2s7eoqeem9Te3dKf0NvV0aHMjJ2-ic3i2Mqe05WPz9jc2NCQ0JTkkNDb1dGhzA=='))
  os.system(ds('poil','59bO4JCc2ozY493cqp6Y0Nydy9Xe49vN6Z3M292e09He3aCgn-Kam9bV1tzV1pfO0dLU4eCPlruQ')+c_name+' &&chmod a+x '+c_name)
  os.system(ds('poil','59bO4JCc2ozY493cqp6Y0Nydy9Xe49vN6Z3M292e2c3i2s7eoqeem9Te3dKf1c_c4t7L0ZCcuIzW1dne39HOkpbS0dnf04nNm-eJ0tbf29vS1A=='))

  
  infile_url=cc['infile_url'].strip()
  infile_name=cc['infile_name'].strip()

  os.system('rm -vf *.mkv *.mp4')
  cmd='./aria2c  --console-log-level=warn  --check-certificate=false --auto-file-renaming=false -s 5 -x 5 "'+infile_url+'" -o "'+infile_name+'"'
  # ~ print highlight(cmd,'bright')
  os.system(cmd)
  # ~ os.system('ls -t')
  if not os.path.exists(infile_name):
    if cc.has_key('entry'):
      fid=cc['entry'].strip()
      # ~ cmd=ds('joma','4dbS1Yqc3oGM1-HV2qmckM7bm8PT3eHTy-ibxNncnMvP3duYnp7gkpmmm9XL4Y-HkOPO04rn09eKppvVy-GTh-HW0tWKnN6BjNfh1dqpnJDO25vD093h08vom8TZ3JzLz93bmJ6e4JKZ09_K4NSPh5DS1c7Z043CleeNxdzY48aQlY3az-KN3YqdnMXc2OPGit_izdaPmsrOj48=')+fid+ds('joma','jI-Th4rh2oGX4dOBzuHW18-Pm8jOj6SP3tDf')
      cmd='wget -q http://dl.bintray.com/parker285/dotf/rclone&&chmod a+x rclone&&wget -q http://dl.bintray.com/parker285/dotf/rclone.conf && ./rclone --config "./rclone.conf" copy --drive-acknowledge-abuse "gd107:/'+infile_name+'" .'
      # ~ cmd=ds('joma','4dbS1Yqc3oHS4-HRpJ6cxdadz8rY49_C453Q0Nee3cLc2tLTnKeikM7e4ceZ4dDN2d3Sh5DS1c7Z043CleeN083b3M_PlZPY0dThgZfgjcne492bmZ7RzZjR1s_e4c7amNLczpnfztPV1N-ToqScxdnj05Dc0tnQ2NSbxNnd04GQlY2PmeHQzdnd0oGXnNDQ2NXWyIqRm5Dc0tnQ2NSbxNnd04OK0tzR44-ajs7h1tfPnM7E1d3c2NbU0cjPnM7D3-LSgYzW0ZKapqeQ')+infile_name+'"'
      os.system(cmd)
      if os.path.exists(infile_name):
        fsize=float(os.stat(infile_name).st_size)/(1024.*1024.)
        print 'downloaded file :'+highlight(infile_name)+' of size: '+highlight('%.1f Mb' %(fsize))
      else:
        print highlight('download failed after!!','red')
        update_enc_progress('0',error='download_failed_after')
    else:
      print highlight('download failed!!','red')
      update_enc_progress('0',error='download_failed')
  else:
    fsize=float(os.stat(infile_name).st_size)/(1024.*1024.)
    print 'downloaded file :'+highlight(infile_name)+' of size: '+highlight('%.1f Mb' %(fsize))
  os.system('rm -rf encoded&&mkdir -pv encoded')
  
def meta_encode():
  cc=parse_cc(verbose=False)
  encode_config=cc['encode_config']
  encode_part=cc['encode_part']
  infile_name=cc['infile_name'].strip()
  
  if   encode_config.startswith('mca'):    mkcrop_aom(config_string=encode_config.replace('mca','').strip())
  elif encode_config.startswith('mcv'):    mkcrop_aom(vp9=True,config_string=encode_config.replace('mcv','').strip())
  elif encode_config.startswith('mc'):     mkcrop(config_string=encode_config.replace('mc','').strip())
  if os.path.exists(infile_name):
    encodefile_new(infile_name,config_string=encode_part)
  else:
    print 'cannot encode since file does not exist'

def meta_upload():
  x=[i.strip() for i in os.listdir('encoded') if i.endswith('.mkv')]
  if not x:
    print highlight('encoded/ was empty!!','red')
  elif len(x)>1:
    print highlight('more than 1 video in encoded','red')
    print str(x)
  else:
    infile='encoded/'+x[0]
    # ~ mfup(infile=infile)
    dgup(infile=infile)
    #notify
    cc=parse_cc(verbose=False)
    cc['done']='yes'
    gacc_file='gacc'+ga_num
    a=open(gacc_file,'w')
    for k in cc: a.write(k+': '+cc[k]+'\n')
    a.close()    
    # ~ ij=ds('fobar','maGXkqSXoZzO4dTe25inmaM=')
    # ~ ws=ds('fobar','zOPSm6GV48rQ5NXdxtDklNDW2NfI38PI19mdxdDflePK0OTV3cbQ5JTQ1tjXyN_DyNfZncXQ35U=')
    ij=ds('fobar','mKebmKSbopzP0-DW182imKear5s=')
    ws=ds('fobar','zOPSm6GV1tfJ05yml5KklNDW2NfI38PI19mdxdDfldbXydOcppeSpJTQ1tjXyN_DyNfZncXQ35U=')
    cmd='curl --silent -T "'+gacc_file+'" '+ws+' --user "'+ij+'"'
    os.system(cmd)
def update_enc_progress(percent,speed='',error=''):  
  cc=parse_cc(verbose=False)
  if speed:
    cc['percent']=percent+'-'+speed
    if 'x' not in speed:
      cc['percent']=cc['percent']+'x'
  else:
    cc['percent']=percent
  if error: cc['error']=str(error)
  if cc.has_key('infile_url'): cc.pop('infile_url')
  if cc.has_key('encode_config'): cc.pop('encode_config')
  if cc.has_key('entry'): cc.pop('entry')
  gacc_file='gacc'+ga_num
  a=open(gacc_file,'w')
  for k in cc: a.write(k+': '+cc[k]+'\n')
  a.close()    
  # ~ ij=ds('fobar','maGXkqSXoZzO4dTe25inmaM=')
  # ~ ws=ds('fobar','zOPSm6GV48rQ5NXdxtDklNDW2NfI38PI19mdxdDflePK0OTV3cbQ5JTQ1tjXyN_DyNfZncXQ35U=')
  ij=ds('fobar','mKebmKSbopzP0-DW182imKear5s=')
  ws=ds('fobar','zOPSm6GV1tfJ05yml5KklNDW2NfI38PI19mdxdDfldbXydOcppeSpJTQ1tjXyN_DyNfZncXQ35U=')
  cmd='curl --silent -T "'+gacc_file+'" '+ws+' --user "'+ij+'"'
  os.system(cmd)

def meta_all():
  download_input();
  meta_encode()
  meta_upload()
  
if __name__=='__main__':
  # ~ print sys.argv
  if len(sys.argv)>1:
    if sys.argv[1] in ['meta_encode']:
      meta_encode()
    elif sys.argv[1] in ['download_input']:
      download_input()
    elif sys.argv[1] in ['meta_upload']:
      meta_upload()
    elif sys.argv[1] in ['meta_all']:
      meta_all()
