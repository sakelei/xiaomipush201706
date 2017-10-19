# xiaomi-push
小米推送服务 Golang SDK

Production ready, full golang implementation of Xiaomi Push API (http://dev.xiaomi.com/console/?page=appservice&mod=push)

```Go
### demo
type PushMessage struct {
	Id             int64  `orm:"pk;auto" json:"id"`
	Title          string `json:"title"`
	SubTitle       string `json:"subtitle"`	
	Describe       string `json:"describe"`
	TargetPushType string `json:"targetPushType"`
	Alias          string `json:"alias"`
	CreateTime     int64  `json:"createTime"`
	TimeToSend     int64 `json:"timeToTend"`
	TimeToLive     int64 `json:"timeToLive"`
	Type      	   string `json:"type"`
	Vid            int64 `json:"vid"`
	Url            string `json:"url"`
	Aid            int64 `json:"aid"`
	Uid            int64 `json:"uid"`
	Cid            int   `json:"cid"`
	AOrder         int64  `json:"aOrder"`
	PassThrough    int    `json:"passThrough"`
}

func AndroidPushMI(pm PushMessage) error {
	beego.Info("pm is:", pm)
	var client = xiaomipush.NewClient(consts.XIAOMI_PUSH_SYCRET, []string{consts.XIAOMI_PUSH_PACKAGENAME})
	var sendErr error
	var msg1 *xiaomipush.Message = xiaomipush.NewAndroidMessage(pm.Title, pm.Describe).SetPayload("this is message")
	timeToEx := time.MilliSecTimeStamp()
	if pm.TimeToSend != Int64NotExist {
		msg1.SetTimeToSend(pm.TimeToSend)
	}
	if pm.TimeToLive != Int64NotExist {
		msg1.SetTimeToLive(pm.TimeToLive*1000, timeToEx)
	}
	msg1.AddExtra("type", pm.Type).AddExtra("vid",strconv.FormatInt(pm.Vid,10)).
		AddExtra("aid",strconv.FormatInt(pm.Aid,10)).AddExtra("cid",strconv.Itoa(pm.Cid)).
		AddExtra("aOrder", strconv.FormatInt(pm.AOrder,10)).AddExtra("url",pm.Url).
		AddExtra("message_uid", strconv.FormatInt(pm.Uid,10))
	if pm.TargetPushType == "ALL" {
		_, sendErr = client.BroadcastAll(context.Background(), msg1)
	} else if pm.TargetPushType == "single" {
		_, sendErr = client.SendToAlias(context.Background(), msg1, pm.Alias)
	}
	return sendErr
}

func IOSPushMI(pm PushMessage) error {
	var client = xiaomipush.NewClient(consts.XIAOMI_PUSH_SYCRET_iOS, []string{consts.XIAOMI_PUSH_PACKAGENAME_iOS})
	var sendErr error
	var msg1 *xiaomipush.Message = xiaomipush.NewIOSMessage(pm.Title,pm.Describe).SetPayload("this is message")
	timeToEx := time.MilliSecTimeStamp()
	if pm.TimeToSend != Int64NotExist {
		msg1.SetTimeToSend(pm.TimeToSend)
	}
	if pm.TimeToLive != Int64NotExist {
		msg1.SetTimeToLive(pm.TimeToLive*1000, timeToEx)
	}
	msg1.AddExtra("type", pm.Type).AddExtra("vid",strconv.FormatInt(pm.Vid,10)).
		AddExtra("aid",strconv.FormatInt(pm.Aid,10)).AddExtra("cid",strconv.Itoa(pm.Cid)).
		AddExtra("aOrder", strconv.FormatInt(pm.AOrder,10)).AddExtra("url",pm.Url)
	if pm.TargetPushType == "ALL" {
		_, sendErr = client.BroadcastAll(context.Background(), msg1)
	} else if pm.TargetPushType == "single" {
		_, sendErr = client.SendToAlias(context.Background(), msg1, pm.Alias)
	}
	return sendErr
}

### Sender APIs

- [x] Send(msg *Message, regID string)
- [x] SendToList(msg *Message, regIDList []string)
- [x] SendTargetMessageList(msgList []*TargetedMessage)
- [x] SendToAlias(msg *Message, alias string)
- [x] SendToAliasList(msg *Message, aliasList []string)
- [x] SendToUserAccount(msg *Message, userAccount string) 
- [x] SendToUserAccountList(msg *Message, accountList []string)
- [x] Broadcast(msg *Message, topic string)
- [x] BroadcastAll(msg *Message) (*SendResult, error)
- [x] MultiTopicBroadcast(msg *Message, topics []string, topicOP TopicOP)
- [x] CheckScheduleJobExist(msgID string)
- [x] DeleteScheduleJob(msgID string) (*Result, error)
- [x] DeleteScheduleJobByJobKey(jobKey string) (*Result, error) 

### Stats APIs

- [x] Stats(start, end, packageName string)
- [x] GetMessageStatusByMsgID(msgID string) (*SingleStatusResult, error)
- [x] GetMessageStatusByJobKey(jobKey string) (*BatchStatusResult, error) 
- [x] GetMessageStatusPeriod(beginTime, endTime int64) (*BatchStatusResult, error) 

### Subscription APIs

- [x] SubscribeTopicForRegID(regID, topic, category string) (*Result, error)
- [x] SubscribeTopicForRegIDList(regIDList []string, topic, category string) (*Result, error)
- [x] UnSubscribeTopicForRegID(regID, topic, category string) (*Result, error)
- [x] UnSubscribeTopicForRegIDList(regIDList []string, topic, category string) (*Result, error)
- [x] SubscribeTopicByAlias(aliases []string, topic, category string) (*Result, error)
- [x] UnSubscribeTopicByAlias(aliases []string, topic, category string) (*Result, error)

### Feedback APIs

- [x] GetInvalidRegIDs() (*InvalidRegIDsResult, error)

### DevTools APIs

- [x] GetAliasesOfRegID(regID string) (*AliasesOfRegIDResult, error)
- [x] GetTopicsOfRegID(regID string) (*TopicsOfRegIDResult, error)
