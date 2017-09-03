<template>
    <div class="container">
        <div classs="row">
            <div class="col-sm-12">
                <div class="panel panel-primary">
                    <div class="panel-heading" id="accordion">
                        <span class="glyphicon glyphicon-comment"></span>
                        Chat
                        <div class="btn btn-group pull-right">
                            <a type="button" class="btn btn-default btn-xa" data-toggle="collapse" data-parent="#accordion" href="#collapseOne">
                                <span class="glyphicon glyphicon-chevron-down"></span>
                            </a>
                        </div>
                    </div>
                    <div class="panel-collapse collapse" id="collapseOne">
                        <div class="panel-body">
                            <ul class="chat">
                                <li v-for="conversation in conversations">
                                    <div class="chat-body clearfix">
                                        <div class="header">
                                            <strong class="primary-font">{{conversation.user}}</strong>
                                        </div>
                                        <p>
                                            {{conversation.message}}
                                        </p>
                                    </div>
                                </li>
                            </ul>
                        </div>
                        <div class="panel-footer">
                            <div class="input-group">
                                <input id="btn-input" type="text" class="form-control input-sm" placeholder="type your message here"
                                       v-model="message" @keyup.enter="store()" autofocus/>
                                <span class="input-group-btn">
                                    <button class="btn btn-warning btn-sm" id="btn-chat" @click.prevent="store()"></button>
                                </span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
    export default{
        data(){
            return {
                conversations : [],
                message :''
            };
        },
        mounted(){
            this.getConversations();
            this.listen();
        },
        methods:{
            store() {
                axios.post('/api/conversations/store',{
                    message:this.message
                })
                    .then((response)=>{
                        this.message = '';
                        this.conversations.push(response.data);
                })
            },
            getConversations(){
                axios.get('/api/conversations',{})
                    .then((response => {
                        this.conversations = response.data;
                    }));
            },
            listen(){
                Echo.channel('public-chat')
                    .listen('NewMessage',(e)=>{
                        this.conversations.push(e.conversation);
                    });
            }
        }
    }
</script>