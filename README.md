# Resolution

[![NPM version](https://img.shields.io/npm/v/@unstoppabledomains/chat.svg?style=flat)](https://www.npmjs.com/package/@unstoppabledomains/chat)

A library for decentralized chat with blockchain domains and gundb

You can use UnstoppableChat in a `<script>` tag from a
[CDN](https://unpkg.com/browse/@unstoppabledomains/chat/build/index.js)

# Architecture

Unstoppable Chat combines the technologies of blockchain domains and [GunDB](https://gun.eco/docs/).

Gundb credentials are made from signing a blockchain domain using the domain owner's private key.

```javascript
const domainName = 'example.crypto';

const username = sign(domainName, privateKey);

const password = sign(username, privateKey);
```

A gundb user is identified by their username and publicKey

The gundb **username** and **publicKey** are found in the blockchain domain's text record gundb.username.value and gundb.public_key.value.
We recommend using the [unstoppabledomains resolution library](https://www.npmjs.com/package/@unstoppabledomains/resolution) to help resolve blockchain domains.

# Initialization

```javascript
const GUNDB_SUPER_PEER = 'https://super2020.herokuapp.com/gun';

const chat = new UnstoppableChat(GUNDB_SUPER_PEER);
```

## Login

```javascript
chat.join(username, password, domainName);
```

## Contacts

```javascript
const contactsListener = await chat.loadContacts();
const contactInvitesListener = await chat.loadContactInvites();

contactsListener.on((contacts) => {
    for (const contact of contacts) {
        const contactMessagesListener = await chat.loadMessagesOfContact(
            contact.pubKey,
            contact.name,
        );
        contactMessagesListener.on((messages) => {});
    }
});

// Auto accept contact invites
contactInvitesListener.on((contactInvites) => {
    for (const contactInvite of contactInvites) {
        chat.acceptContactInvite(
            contactInvite.alias,
            contactInvite.pubKey,
            contactInvite.name,
        );
    }
});

chat.addContact(username, publicKey, domainName);
chat.sendMessageToContact(publicKey, message);
```

## Channels

```javascript
const channelsListener = await chat.loadChannels();
const channelInvitesListener = await chat.loadChannelInvites();

channelsListener.on((channels) => {});
channelInvitesListener.on((channelInvites) => {
  for (const channelInvite of channelInvites) {
    chat.acceptChannelInvite(channelInvite) ||
      chat.denyChannelInvite(channelInvite);
  }
});

const channel = await chat.createChannel(channelName);

chat.inviteToChannel(channel, username, publicKey, domainName);
chat.leaveChannel(channel);
```

## Announcements

```javascript
const announcementsListener = await chat.loadAnnouncements();
const announcementInvitesListener = await chat.loadAnnouncementInvites();

announcementsListener.on((announcements) => {});
announcementInvitesListener.on((announcementInvites) => {
  for (const announcementInvite of announcementInvites) {
    chat.acceptAnnouncementInvite(announcementInvite) ||
      chat.denyAnnouncementInvite(announcementInvite);
  }
});

const announcement = await chat.createAnnouncement(announcementName);

chat.inviteToAnnouncement(announcement, username, publicKey, domainName);
chat.leaveAnnouncement(announcement);
```
