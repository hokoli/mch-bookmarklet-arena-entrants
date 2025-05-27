# 🎮 MCH Arena Entrants Extractor

My Crypto Heroes（MCH）のアリーナ・トーナメント参加者のデータを取得し、CSVファイルとしてダウンロードできるブックマークレットです。

## 🚀 ブックマークレット追加

下のリンクをブックマークバーにドラッグ&ドロップしてください：

**👉 [MCH Arena Entrants](javascript:(async function(){function getUserListFromArena(){return Array.from(document.querySelectorAll('.groupUserList__item')).map(item=>{const spans=item.querySelectorAll('span');if(spans.length>=2){return{userName:spans[0].textContent.trim(),userId:spans[1].textContent.trim().replace('#','')}}return null}).filter(user=>user&&user.userName&&user.userId)}function getUserListFromTournament(){const tournamentRounds=document.querySelectorAll('.tournament__tournament__round');if(tournamentRounds.length===0)return[];const firstRound=tournamentRounds[0];const userElements=firstRound.querySelectorAll('.userName');return Array.from(userElements).map(el=>{const link=el.closest('a');if(!link||link.querySelector('.tournamentMatch__user--empty'))return null;const nameSpan=el.querySelector('.userName__name');const uidSpan=el.querySelector('.userName__uid');if(nameSpan&&uidSpan){return{userName:nameSpan.textContent.trim(),userId:uidSpan.textContent.trim().replace('#','')}}return null}).filter(user=>user&&user.userName&&user.userId)}function getUserList(){const arenaUsers=getUserListFromArena();if(arenaUsers.length>0)return arenaUsers;return getUserListFromTournament()}async function getUserAddress(userId){try{const response=await fetch(`https://www.mycryptoheroes.net/api/proxy/mch/users/${userId}`);if(response.ok){const data=await response.json();return data.user_data?.eth||''}return''}catch(error){console.warn(`アドレス取得エラー (User ${userId}):`,error);return''}}async function getYukichiCoins(address,userId){return''}async function getInuBalance(address,userId){try{const tokenContract='0x6b7b5F6D7411F374694595d05719ad2f060aAC61';const balanceOfData='0x70a08231'+address.substring(2).padStart(64,'0');const response=await fetch('https://rpc.mainnet.oasys.games',{method:'POST',headers:{'Content-Type':'application/json'},body:JSON.stringify({jsonrpc:'2.0',method:'eth_call',params:[{to:tokenContract,data:balanceOfData},'latest'],id:1})});if(response.ok){const data=await response.json();if(data.result&&data.result!=='0x'&&data.result!=='0x0'){const balanceHex=data.result;const balanceBigInt=BigInt(balanceHex);return Math.floor(Number(balanceBigInt)).toString()}}return'0'}catch(error){console.warn(`InuBalance取得エラー (User ${userId}):`,error);return'0'}}function downloadCSV(users){const csvHeader='ユーザID,ユーザ名,アドレス,yukichi発行コイン,InuBalance\n';const csvRows=users.map(user=>`${user.userId},"${user.userName}","${user.address}","${user.yukichiCoin}",${user.inuBalance}`).join('\n');const csvContent=csvHeader+csvRows;const blob=new Blob(['\uFEFF'+csvContent],{type:'text/csv;charset=utf-8;'});const link=document.createElement('a');const url=URL.createObjectURL(blob);link.setAttribute('href',url);link.setAttribute('download',`mch_users_${new Date().toISOString().split('T')[0]}.csv`);link.style.visibility='hidden';document.body.appendChild(link);link.click();document.body.removeChild(link)}const userList=getUserList();if(userList.length===0){alert('ユーザーリストが見つかりません。正しいページでブックマークレットを実行してください。');return}const loadingDiv=document.createElement('div');loadingDiv.style.cssText=`position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); background: rgba(0,0,0,0.8); color: white; padding: 20px; border-radius: 10px; z-index: 10000; font-family: Arial, sans-serif;`;loadingDiv.innerHTML='<div>データを取得中...</div><div id="progress">0/0</div>';document.body.appendChild(loadingDiv);const users=[];const progressDiv=document.getElementById('progress');for(let i=0;i<userList.length;i++){const user=userList[i];progressDiv.textContent=`${i+1}/${userList.length} - ユーザー ${user.userId} を処理中...`;const address=await getUserAddress(user.userId);let yukichiCoin='';let inuBalance='0';if(address){yukichiCoin=await getYukichiCoins(address,user.userId);inuBalance=await getInuBalance(address,user.userId)}users.push({userId:user.userId,userName:user.userName,address:address,yukichiCoin:yukichiCoin,inuBalance:inuBalance});await new Promise(resolve=>setTimeout(resolve,100))}downloadCSV(users);document.body.removeChild(loadingDiv);alert(`${users.length}人のデータをCSVでダウンロードしました。`);})()) ← このリンクをドラッグ**

## 🔧 使用方法

1. 上のリンクをブックマークバーにドラッグ&ドロップ
2. MCHのアリーナまたはトーナメントページを開く
3. ブックマークから「MCH Arena Entrants」をクリック
4. CSVファイルが自動ダウンロードされます

## 📊 取得データ

- ユーザーID
- ユーザー名  
- ウォレットアドレス
- yukichi発行コイン（空白）
- InuBalance（MCHINUトークン残高）

---

⚠️ 自己責任でご利用ください。MCHの利用規約を遵守してください。
