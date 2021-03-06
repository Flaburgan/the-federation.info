<template>
    <main>
        <header class="main-header">
            <div class="main-title">
                <h1><router-link to="/">The Federation</router-link></h1>
                <h2>Welcome to the new social web</h2>
            </div>
            <div class="flex">
                <div class="col4">
                    <div class="tile valign-wrapper">
                        <ApolloLoader :loading="$apollo.loading">
                            {{ protocols.length || '' }} <strong>Protocols</strong>
                        </ApolloLoader>
                    </div>
                </div>
                <div class="col4">
                    <div class="tile valign-wrapper">
                        <ApolloLoader :loading="$apollo.loading">
                            {{ platforms.length || '' }} <strong>Projects</strong>
                        </ApolloLoader>
                    </div>
                </div>
                <div class="col4">
                    <div class="tile valign-wrapper">
                        <ApolloLoader :loading="$apollo.loading">
                            {{ nodes.length || '' }} <strong>Nodes</strong>
                        </ApolloLoader>
                    </div>
                </div>
                <div class="col4">
                    <div class="tile valign-wrapper">
                        <ApolloLoader :loading="$apollo.loading">
                            {{ statsGlobalToday ? statsGlobalToday.usersTotal : '' }} <strong>Users</strong>
                        </ApolloLoader>
                    </div>
                </div>
            </div>
        </header>

        <section class="tile">
            <header>
                <h2>What is The Federation?</h2>
            </header>
            <div>
                <div class="flex">
                    <div class="col2">
                        <p>
                            The Federation refers to a global social network composed of nodes that talk to each other.
                            Each of them is an installation of software which supports one of the federated social web
                            protocols.
                        </p>
                        <div class="center">
                            <router-link
                                to="/nodes"
                                class="btn btn-primary"
                            >
                                Nodes list
                            </router-link>
                        </div>
                    </div>
                    <div class="col2">
                        <ApolloLoader :loading="$apollo.loading">
                            <ul>
                                <li>
                                    Nodes:
                                    <strong>{{ nodes.length || '' }}</strong>
                                </li>
                                <li>
                                    Users:
                                    <strong>{{ statsGlobalToday ? statsGlobalToday.usersTotal : '' }}</strong>
                                </li>
                                <li>
                                    Last 6 months active users:
                                    <strong>{{ statsGlobalToday ? statsGlobalToday.usersHalfYear : '' }}</strong>
                                </li>
                                <li>
                                    Last month active users:
                                    <strong>{{ statsGlobalToday ? statsGlobalToday.usersMonthly : '' }}</strong>
                                </li>
                                <li>
                                    Posts:
                                    <strong>{{ statsGlobalToday ? statsGlobalToday.localPosts : '' }}</strong>
                                </li>
                                <li>
                                    Comments:
                                    <strong>{{ statsGlobalToday ? statsGlobalToday.localComments : '' }}</strong>
                                </li>
                            </ul>
                        </ApolloLoader>
                        <div>
                            <strong>Disclaimer:</strong> These counts do not reflect the whole network due to the
                            opt-in nature of the statistics.
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <Charts />

        <div id="projects" />
        <section class="tile">
            <header>
                <h2>Projects</h2>
            </header>
            <div class="overflow-x">
                <table>
                    <thead>
                        <tr>
                            <th>Project</th>
                            <th>Nodes</th>
                            <th>Users</th>
                            <th>Website</th>
                            <th>Code</th>
                        </tr>
                    </thead>
                    <tbody>
                        <PlatformTableRow
                            v-for="platform in platforms"
                            :key="platform.id"
                            :platform="platform"
                            :nodes="nodes"
                        />
                    </tbody>
                </table>
                <ApolloLoader :loading="$apollo.loading" />
            </div>
        </section>

        <div id="protocols" />
        <section class="tile">
            <header>
                <h2>Protocols</h2>
            </header>
            <div class="overflow-x">
                <table>
                    <thead>
                        <tr>
                            <th>Protocol</th>
                            <th>Nodes</th>
                            <th>Users</th>
                        </tr>
                    </thead>
                    <tbody>
                        <ProtocolTableRow
                            v-for="protocol in protocols"
                            :key="protocol.id"
                            :protocol="protocol"
                        />
                    </tbody>
                </table>
                <ApolloLoader :loading="$apollo.loading" />
            </div>
        </section>

        <section class="tile">
            <header>
                <h2>Be part of our world</h2>
            </header>
            <div class="flex">
                <div class="col2">
                    <h3>Host yourself</h3>
                    <p>
                        You want to install your own node? Awesome! Choose a project and follow an installation guide.
                    </p>
                    <div id="install-guides-list">
                        <div
                            v-for="platform in platforms"
                            :key="platform.id"
                        >
                            <div v-if="platform.installGuide">
                                <a
                                    :href="platform.installGuide"
                                    target="_blank"
                                    rel="noopener noreferrer"
                                >
                                    {{ platform.displayName ? platform.displayName : platform.name }}
                                </a>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="col2">
                    <h3>I already have my node!</h3>
                    <p>
                        Yay! Welcome to the crew! To include your node on this website, please go to
                        <code>https://the-federation.info/register/&lt;yournode.tld&gt;</code>.
                        After some seconds, you should see your node added..
                    </p>
                </div>
            </div>
        </section>
    </main>
</template>

<script>
import gql from 'graphql-tag'

import ApolloLoader from "./common/ApolloLoader"
import Charts from "./Charts"
import PlatformTableRow from "./PlatformTableRow"
import ProtocolTableRow from "./ProtocolTableRow"

const query = gql`
    query {
        nodes {
            id
            platform {
                name
            }
        }
        platforms {
            id
            code
            name
            displayName
            installGuide
            license
            website
        }
        protocols {
            id
            name
            nodes {
              name
            }
        }
        statsGlobalToday {
            usersTotal
            usersHalfYear
            usersMonthly
            localPosts
            localComments
        }
    }
`


export default {
    apollo: {
        allQueries: {
            query,
            result({data}) {
                this.nodes = data.nodes
                this.platforms = data.platforms
                this.protocols = data.protocols
                this.statsGlobalToday = data.statsGlobalToday
            },
            manual: true,
        },
    },
    name: "IndexContent",
    components: {ApolloLoader, Charts, PlatformTableRow, ProtocolTableRow},
    data() {
        return {
            nodes: [],
            platforms: [],
            protocols: [],
            statsGlobalToday: [],
        }
    },
}
</script>

<style scoped>
    #install-guides-list {
        text-align: center;
    }
</style>
